# requestClient

# Explainer

Dynamically set Web page, `Worker`, `SharedWorker`, `Worklet` as a `Client` or `WindowClient` of `ServiceWorker`.

There is currently no way to dynamically set a Client or WindowClient of a ServiceWorker to a Web page, Worker, SharedWorker, or Worklet not in the scope of a ServiceWorker.

That becomes problematic for MV3 extension ServiceWorker's in particular and Web pages in general that want to establish a bi-directional communication channel with ServiceWorker, see https://github.com/GoogleChrome/chrome-extensions-samples/issues/766.

Workarounds include creating a iframe or using window.open(), or an offscreen document with WebRTC - however that requires loading an entire HTML document just to pass transferable data to and fro - and as pointed out in the above issue, an iframe can be remove from the document by other code.

I propose in the ServiceWorker something like what 

```
await registration.showNotification('test', {data: new Blob(['test'], {type:'text/plain'})});
```

does, where when the user opts-in the Web page is dynamically set as a WindowClient of the ServiceWorker, thereafter the Web page and ServiceWorker can communicate via onmessage and postMessage(). Notice that the `data` property is completely useless in that code https://www.reddit.com/r/javascript/comments/zohvh9/askjs_what_is_the_purpose_of_notificationdata_in/?utm_source=share&utm_medium=web2x&context=3 - we can't get the data on the Web page, if we could that would be an alternative for one-way communication or transfer of data.

Similarly, the Web page can request permission to become a WindowClient of the ServiceWorker via something like

```
await navigator.permissions.request({name:'window-client-<origin-of-active-service-worker>'}).
```

I posted this question https://www.reddit.com/r/javascript/comments/zv4v4e/askjs_what_are_the_worst_case_scenarios_for/ to get feedback on the worst case scenario for dynamically setting a Client or WindowClient. I cannot fathom a case where something can go horribly wrong. We already have clipboard permission, which sets clipboard data for the entire OS.
