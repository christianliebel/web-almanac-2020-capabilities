# Capabilities

The objective of your chapter is to write a data-driven/research-based answer/blog post to this big question: “What is the state of web capabilities in 2020?”

[Author's Guide](https://github.com/HTTPArchive/almanac.httparchive.org/wiki/Authors'-Guide#writing-the-chapter) ::
[Google Style Guide](https://developers.google.com/style/highlights) ::
[Results](https://docs.google.com/spreadsheets/d/1N6j1qKv7vc51p1W9z_RrbJX9Se3Pmb-Uersfz4gWqqs/edit#gid=2077755325) ::
[Data Viz Gallery (Template)](https://docs.google.com/spreadsheets/d/1OtE2wqjGOMy7pRi3uorK7ITVDJXAVoP-r69k8eeKzeU/edit?ts=5f5e8838#gid=0)

---

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Introduction](#introduction)
- [Clipboard Access](#clipboard-access)
  - [Read Access](#read-access)
  - [Write Access](#write-access)
- [Storage](#storage)
  - [Estimate the available storage](#estimate-the-available-storage)
  - [Opt-in to persistent storage](#opt-in-to-persistent-storage)
- [Notifications](#notifications)
  - [Badging](#badging)
  - [Scheduling Notifications](#scheduling-notifications)
- [Wake Lock API](#wake-lock-api)
- [Idle Detection API](#idle-detection-api)
- [Periodic Background Sync](#periodic-background-sync)
  - [Register for periodic sync](#register-for-periodic-sync)
  - [Respond to a periodic sync interval](#respond-to-a-periodic-sync-interval)
- [Integration with native app stores](#integration-with-native-app-stores)
- [Content Indexing](#content-indexing)
- [Transport](#transport)
  - [Backpressure for WebSockets](#backpressure-for-websockets)
  - [Make it QUIC](#make-it-quic)
- [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

The web platform has advanced to an application platform.
With the appearance of HTML5, web applications 
cross-platform 😎TODO
spec status 😎TODO

However, there is still a gap between the capabilities of native applications and web apps, the so called app gap.
With the Web Capabilities project, also known as Project Fugu, the Chromium contributors try to close the app gap by evaluating, specifying, and implementing APIs in browsers with native power.

[Progressive Web Apps](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) have
technical foundations such as Service Workers, the Web App Manifest, and IndexedDB. 
Applications can run offline.
However, web applications can only call native functionality
😎TODO

Progressive Enhancement / feature Detection 😎TODO

Special emphasis on security and privacy 😎TODO
Most APIs require the website to be sent over a secure connection (HTTPS).
Some of them require a user gesture, such as a click or key press, to prevent fraud (ads).

In this chapter, we'll have a look at different modern web APIs, and the state of web capabilities in 2020. 

TODO OVERALL:
Links to specs? 😎TODO
Nicer headlines? 😎TODO
Use cases? 😎TODO

## Clipboard Access

With the help of the `document.execCommand()` method, websites could already access with the user's clipboard.
However, this approach is somewhat restricted, as the API is synchronous (😎TODO: this is mostly a problem with re-encoding of large images to prevent image bombs), and it can only interact with selected text in the DOM.
This is where the [Async Clipboard API](https://web.dev/async-clipboard/) comes in.
The new API is not only asynchronous, meaning it doesn't block the page for large chunks of data, but also allows for images to be copied to or pasted from the clipboard in supported browsers.

### Read Access

```
(🐡 Launched) Async Clipboard Read
```

The Async Clipboard API provides two methods for reading content from the clipboard:
A shorthand method for plain text, called `navigator.clipboard.readText()`, and a method for arbitrary data, called `navigator.clipboard.read()`.
Currently, most browsers only support PNG images (TODO: True?).
Due to privacy reasons, reading from the clipboard always requires the user's consent.

(TODO: Analyze data)

### Write Access

```
(🐡 Launched) Async Clipboard Write
```

Apart from reading operations, the Async Clipboard API also offers two methods for writing content to the clipboard.
Again, there's a shorthand method for plain text, called `navigator.clipboard.writeText()`, and one for arbitrary data called `navigator.clipboard.write()`.
In Chrome, writing to the clipboard while the browsing context is active (i.e., tab is open or window 😎TODO) does not require permission.
Trying to write to the clipboard when the website is in the background does, however.

(TODO: Analyze data)

With the Raw Clipboard Access API, the Async Clipboard API should be further enhanced by allowing arbitrary data to be copied from or pasted to the clipboard. (TODO: More Info)

## Storage

Web browsers allow you to store data on the user's system in different ways, such as Cookies, in the Indexed Database (IndexedDB), the Service Worker's Cache Storage or Web Storage (Local Storage, Session Storage).
In modern browsers, you can easily store hundreds of megabytes and even more depending on the browser.
When browsers run out of space, they can clear data until the system is no longer over the limit, which can lead to data loss.

Thanks to the [StorageManager API](https://web.dev/storage-for-the-web/#check-available), browsers no longer behave like a black box in that regard:
This API allows you to estimate the remaining space available, and to opt-in to persistent storage, meaning that the browser will not clear your website's data when disk space is low.
Therefore, the API introduces a new `StorageManager` interface on the `navigator` object.

### Estimate the available storage

```
(🐡 Launched) Storage Estimation
```

You can have the browser estimate the available storage by calling `navigator.storage.estimate()`.
This method returns a promise resolving to an object with two properties:
`usage` shows the number of bytes used by the application and `quota` contains the maximum number of bytes available.

(TODO: Analyze)

### Opt-in to persistent storage

```
(🐡 Launched) Persistent Storage
```

There are two categories of web storage: "Best Effort" and "Persistent", with the first being the default.
When a device is low on storage, the browser automatically tries to free up best effort storage.
For instance, Firefox and Chromium-based browsers evict storage from the least recently used origins.
This, however, poses a risk of losing critical data.
To prevent eviction, you can opt for persistent storage.
In this case, the browser will not clear the storage, even when space is low.
Users can still choose to clear up the storage manually.
To opt for persistent storage, you call the `navigator.storage.persist()` method.
Depending on the browser and site engagement, a permission prompt may show, or the request will automatically be accepted or denied.

(TODO: Analyze)

## Notifications

With the help of the Push and Notifications APIs, web applications have long been able to receive push messages and display notification banners.
However, some parts are missing:
Up until now, push messages had to be sent via the server; they could not be scheduled offline.
Also, web applications installed to the system could not show badges on their icon.
The Badging and Notification Triggers APIs enable both scenarios.

### Badging

```
(🐡 Launched) Badge Set
(🐡 Launched) Badge Clear
```

On several platforms, it's common for applications to present a badge on the application's icon indicating the amount of open actions:
For instance, the badge could show the number of unread emails, notifications, or to-do items to complete.
The [Badging API](https://web.dev/badging-api/) allows your installed web application to show such a badge on its icon.
By calling `navigator.setAppBadge()`, you can set the badge.
This method takes a number to be shown on the application's badge.
The browser then takes care of displaying the closest possible representation on the user's device.
If you don't specify a number, a generic badge will be shown (e.g., a white dot on macOS).
Calling `navigator.clearAppBadge()` removes the badge again.
Badging API is a great choice for email clients, social media apps, or messengers.

(TODO: analyze data)
(TODO: Twitter?)

### Scheduling Notifications

```
(🧪 Origin trial) Notification Triggers
```

The Push API requires the user to be online to receive a notification.
Some applications, such as games, reminder or ToDo apps, calendars, or alarm clocks, could also determine the target date for a notification locally and schedule it.
To allow this feature, the Chrome team experimented with a new API called [Notification Triggers](https://web.dev/notification-triggers/).
This API adds a new property called `showTrigger` to the `options` map that can be passed to the `showNotification()` method on the Service Worker's registration.
The API is designed to allow for different kinds of triggers in the future,
albeit for now, only time-based triggers are implemented.
For scheduling a notification based on a certain date and time, you can create a new instance of a `TimestampTrigger` and pass the target timestamp to it:

```js
registration.showNotification('Title', {
  body: 'Message',
  showTrigger: new TimestampTrigger(timestamp)
});
```

(TODO: analyze data)

Due to the lack of robust feedback, the Chrome team decided to temporarily pause further development of Notification Triggers in July 2020.

## Wake Lock API

```
(🐡 Launched) Screen Wake Lock
```

To save energy, mobile devices darken the screen backlight and eventually turn off the device's display, which makes sense in most cases.
However, there are scenarios where the user may want the application to explicitly keep the display awake, for instance, when reading a recipe while cooking or watching a presentation.
The [Wake Lock API](https://web.dev/wakelock/) solves this problem by providing you a mechanism to keep the screen on.

To obtain a wake lock, you call the `navigator.wakeLock.request()` method.
This method takes a WakeLockType parameter. 
In the future, the Wake Lock API could provide other lock types, such as turning the screen off, but keeping the CPU on.
For now, the API only supports screen locks, so you need to pass the argument `'screen'` to the method.
The method returns a promise that resolves to a WakeLockSentinel object.
To release the screen wake lock later on, you call the `release()` method on this object, so you need to make sure to store the reference.
The browser will automatically release the lock when the tab is inactive, or the user minimizes the window.
Also, the browser may deny your request and reject the promise, for example due to low battery.

(TODO: analyze data)

In a [Wake Lock API case study on BettyCrocker.com](https://web.dev/betty-crocker/), a popular cooking website in the US, the purchase intent indicators increased by about 300 percent. This shows… (TODO: More)

## Idle Detection API

```
(🧪 Origin trial) Idle Detection
```

Some applications need to determine if the user is actively using a device or if they are idle.
For instance, chat applications may display that the user is absent
There are various factors that can be taken into account, such as a lack of interaction with the screen, mouse, or keyboard.
The [Idle Detection API](https://web.dev/idle-detection/) provides an abstract API that allows you to check if either the user or screen is idle given a certain threshold.

To do so, the API provides a new `IdleDetector` interface on the global `window` object.
Before you can use this functionality, you have to request permission by calling `IdleDetector.requestPermission()` first.
If the user grants the permission, you can create a new instance of `IdleDetector`.
This object provides two properties, `userState` and `screenState` containing the respective idle states.
It will raise a `change` event when either the user's, or the screen's idle state change.
Finally, the idle detector needs to be started by calling its `start()` method.
The method takes a configuration object with two parameters:
A `threshold` defining the time in milliseconds that the user has to be idle.
The minimum threshold is 60,000 milliseconds (one minute).
Optionally, you can pass an AbortSignal to the `abort` property, which you can use to abort idle detection later on.

(TODO: analyze data)

At the time of this writing, the Idle Detection API is under origin trial, so its API shape may change in the future.

## Periodic Background Sync

```
(🐡 Launched) Periodic Background Sync Register
(🐡 Launched) Periodic Background Sync
```

When the user closes a web application, it cannot communicate with its backend service anymore.
In some cases, you might still want to synchronize data on a more or less regular basis, just as native applications can.
For instance, news applications might want to download the latest headlines before the user wakes up.
The [Periodic Background Sync API](https://web.dev/periodic-background-sync/) wants to bridge this gap between web and native.

### Register for periodic sync

Periodic background sync relies on Service Workers—a piece of JavaScript registered by a web application, that can run even when the app is closed.
As other capabilities, this API requires permission first.
The API implements a new interface called `PeriodicSyncManager`.
If present, you can access an instance of this interface on the Service Worker's registration.
To synchronize data in the background, the application has to register first, by calling `periodicSync.register()` on the registration.
This method takes two parameters:
A tag, which is an arbitrary string to recognize the registration again later on.
The second one is a configuration object that takes a `minInterval` property.
This property defines the minimum interval in milliseconds, the browser ultimately decides how often it will invoke background synchronization:

```
registration.periodicSync.register('articles', {
  minInterval: 24 * 60 * 60 * 1000 // one day
});
```

### Respond to a periodic sync interval

For each tick of the interval, and if the device is online, the browser triggers the Service Worker's `periodicsync` event.
Then, the Service Worker script can perform the necessary steps to synchronize the data:

```
self.addEventListener('periodicsync', (event) => {
  if (event.tag === 'articles') {
    event.waitUntil(syncStuff());
  }
});
```

(TODO: analyze data)

At the time of this writing, only Chromium-based browsers implement this API.
On these browsers, the application has to be installed (i.e., added to the homescreen) first, before the API can be used.
The site engagement score of the website defines if and how often periodic sync events can be invoked.
In the best case, websites can sync content once a day.

TODO: Too long?

## Integration with native app stores

```
(🐡 Launched) Get Installed Related Apps
```

Progressive Web Apps are a great application model.
However, in some cases, it may still make sense to offer a separate native application:
For example, if the app needs to use features that are not available on the web, or based on the experience of the team.
When the user already has your native app installed, you might not want to send notifications twice or promote the installation of your PWA.

To detect if the user has already your native application or PWA on the system, you can use the [getInstalledRelatedApps() method](https://web.dev/get-installed-related-apps/) on the `navigator` object.
This method is currently provided by Chromium-based browsers and works for both Android and Universal Windows Platform (UWP) apps.
You need to adjust the native app bundles to refer to your website, and you need to add information about your native apps to the Web App Manifest of your PWA.
Calling the `getInstalledRelatedApps()` method will then return the list of your apps installed on the user's device:

```
const relatedApps = await navigator.getInstalledRelatedApps();
relatedApps.forEach((app) => {
  console.log(app.id, app.platform, app.url);
});
```

(TODO: Analyze data)

## Content Indexing

```
(🧪 Origin trial) Content Indexing
```

Web apps can store content offline using various ways, such as the Cache Storage, or IndexedDB.
However, for users it's hard to discover which content is available offline.
The [Content Indexing API](https://web.dev/content-indexing-api/) allows you to display your content more prominently.
Currently, this API is only supported by Chrome on Android.
This browser shows a list of "Articles for you" in the Downloads menu.
Content indexed via the Content Indexing API will appear there.

The Content Indexing API extends the Service Worker API by providing a new `ContentIndex` interface.
This interface is available via the `index` property of the Service Worker's registration.
The `add()` method allows you to add content to the index:
Each piece of content must have an ID, a URL, a launch URL, title, description and a set of icons.
Optionally, the content can be grouped into different categories, such as articles, homepages, or videos.
The `delete()` method allows you to remove content from the index again, and the `getAll()` method returns a list of all indexed entries.

(TODO: Analyze data)

## Transport

Finally, there are two new transport methods that are currently in origin trial.
The first one allows you to receive high-frequency messages with WebSockets, while the second one introduces an entirely new bidirectional communication protocol apart from HTTP and WebSockets.

### Backpressure for WebSockets

The WebSocket API is a great choice for bidirectional communication between your website and your server.
However, the WebSocket API does not allow for backpressure, so applications dealing with high-frequency messages may freeze.
The [WebSocketStream](https://web.dev/websocketstream/) API wants to bring easy-to-use backpressure support to the WebSocket API by extending it with streams.
Instead of using the usual `WebSocket` constructor, you need to create a new instance of the `WebSocketStream` interface.
The `connection` property of the stream returns a promise that resolves to a readable and writable stream that allow you to obtain a stream reader or writer, respectively:

```
const wss = new WebSocketStream(WSS_URL);
const {readable, writable} = await wss.connection;
const reader = readable.getReader();
const writer = writable.getWriter();
```

The WebSocketStream API transparently solves backpressure for you, as the stream readers and writers will only proceed if its safe to do so.

(TODO: Analyze)

### Make it QUIC

[QUIC](https://www.chromium.org/quic) is a multiplexed, stream-based, bidirectional transport protocol implemented on UDP.
It's an alternative to HTTP/WebSocket APIs that are implemented on TCP.
The [QuicTransport API](https://web.dev/quictransport/) is the client-side API for sending messages to and receiving messages from a QUIC server.
You can choose to send data unreliably via datagrams, or reliably by using its streams API:

```
const transport = new QuicTransport(QUIC_URL);
await transport.ready;

const ws = transport.sendDatagrams();
const stream = await transport.createSendStream();
```

QuicTransport is a great alternative to WebSockets, as it supports the use cases from the WebSocket API, and adds support for scenarios where minimal latency is more important than reliability and message order.
This makes it a great choice for games and applications dealing with high-frequency events.

(TODO: Analyze)

## Conclusion

The state of web capabilities 2020 is… (TODO)

- security
- privacy
- developer interaction
- browser support
