Q&A....
===

Server side hub events (OnReconnected/OnDisconnected/OnConnected) not fired
---------------------------------------------------------------------------

**Solution**

```
hub = $.connection.myHub;
            // dummy call to force the [OnConnected()] method on signalR hub
            hub.client.foo = function () { };
            $.connection.hub.start().done( ... )
            
```
**Refs**

ref: http://elbruno.com/2014/07/25/signalr-error-html-client-does-not-raise-onconnected-method-on-server-hub-2/


DI not working
--------------

**Solution**



Bootstrap/Unity Config

```
container.RegisterType<IHubActivator, UnityHubActivator>(new ContainerControlledLifetimeManager());
GlobalHost.DependencyResolver.Register(typeof(IHubActivator), () => new UnityHubActivator(container));
```

UnityHubActivator.cs
```
using System;
using Microsoft.AspNet.SignalR.Hubs;
using Microsoft.Practices.Unity;

namespace DerivcoApps.Alerter.WebApi.Infrastructure
{
  public class UnityHubActivator : IHubActivator
  {
    private readonly IUnityContainer _container;

    public UnityHubActivator(IUnityContainer container)
    {
      _container = container;
    }

    public IHub Create(HubDescriptor descriptor)
    {
      if (descriptor == null)
      {
        throw new ArgumentNullException("descriptor");
      }

      if (descriptor.HubType == null)
      {
        return null;
      }

      object hub = _container.Resolve(descriptor.HubType) ?? Activator.CreateInstance(descriptor.HubType);
      return hub as IHub;
    }
  }
}
```





**Refs**

ref: http://elbruno.com/2014/07/25/signalr-error-html-client-does-not-raise-onconnected-method-on-server-hub-2/


Disocnnections use cases
------------------------



When I Mention the following i am referring to the server side Hub method - OnConnected
- OnDisconnected
- OnReconnected

1)

```
0:00 - Client connects to server, OnConnected is triggered
0:10 - Client loses connection due to ISP issues (and realizes it loses connection)
0:15 - Client Regains connectivity
0:16 - OnReconnected event is triggered
```

2)
```
0:00 - Client connects to server, OnConnected is triggered
0:10 - Client loses connection due to pulling ethernet cable (doesn't realize it's disconnected)
0:15 - Client Regains connectivity
Two things can happen here
A: 0:16 - Nothing happens and client continues on with its previous connection
B: 0:~45 - Client Realizes its disconnected *
B: 0:46 - Client transitions into the reconnecting state
B: 0:47 - Client successfully reconnects and the OnReconnected event is triggered.
```
3)
```
0:00 - Client connects to server, OnConnected is triggered
0:10 - Client loses connection due to pulling ethernet cable (doesn't realize it's disconnected)
0:~45 - Client Realizes its disconnected *
0:46 - Client transitions into the reconnecting state
1:15 - Server determines that client has been gone for too long and then forgets about it, queuing up a "disconnect" command for the client to receive if it reconnects slightly later. ***
1:15 - OnDisconnected is triggered
1:16 - Client regains connectivity
1:17 - Client does a "soft" reconnect (does not trigger OnReconnected)
1:18 - Client retrieves the "disconnect" command
1:19 - Client calls "stop" and does a soft disconnect (does not trigger OnDisconnected)
```
4)
```
0:00 - Client connects to server, OnConnected is triggered
0:10 - Client loses connection due to pulling ethernet cable (doesn't realize it's disconnected)
0:~45 - Client Realizes its disconnected *
0:46 - Client transitions into the reconnecting state
1:15 - Server determines that client has been gone for too long and then forgets about it, queuing up a "disconnect" command for the client to receive if it reconnects slightly later. ***
1:15 - OnDisconnected is triggered
1:30 - Client stops trying to reconnect (been trying too long) **
1:30 - Client transitions into disconnected state
```

* Due to client side keep alive check: Used to determine when a client is offline due to lack of keep alives. Not utilized for the long polling transport

** Due to client side disconnect timeout: Used to determine when a client has been reconnecting for too long of a period and chances are the server has forgotten about the client during the time

*** Due to server disconnect timeout: Used to determine when a client should be forgotten about. It's a time span that starts accruing once a connection is tagged as dead on the server. Ultimately the server queues a disconnect command for the client's topic which tells the client (if it reconnects) that it needs to start a fresh connection. The command will disappear from the server when the topic is cleaned up.

Hope this helps!



Transport protocols
===================

**Refs**

- http://blog.pusher.com/what-came-before-websockets/
- http://caniuse.com/cors


Websockets [webSockets]
-----------------------

**Prerequisite**

- IIS 8+
- Latest version of any modern browser, IE10+
- CORS : Cross Resource Sharing Enabled

***web.config***

```

  <system.webServer>

      <modules runAllManagedModulesForAllRequests="true">
      </modules>
    
  </system.webServer>
  
```

***IIS Setup***


1. "Turn Windows features on or off" 
2. Internet Information Services
3. World Wide Web Services
4. Application Development Features
5. WebSocket Protocol (or the equivalent if you are on a server SKU).


ServerSourceEvent (SSE) [serverSentEvents]
------------------------------------------

**Prerequisite**

- FF, Chrome, (anything but IE)





foreverFrame


longPolling
