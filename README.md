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
