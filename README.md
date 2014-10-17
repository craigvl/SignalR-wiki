Q&A
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
