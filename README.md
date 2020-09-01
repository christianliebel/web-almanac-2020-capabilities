# Capabilities

The objective of your chapter is to write a data-driven/research-based answer/blog post to this big question: “What is the state of web capabilities in 2020?”

HTTPS, Security and Privacy
Progressive Enhancement / feature Detection

https://github.com/HTTPArchive/almanac.httparchive.org/wiki/Authors'-Guide#writing-the-chapter

https://developers.google.com/style/highlights

https://docs.google.com/spreadsheets/d/1N6j1qKv7vc51p1W9z_RrbJX9Se3Pmb-Uersfz4gWqqs/edit#gid=2077755325

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Introduction](#introduction)
- [Web Share API](#web-share-api)
- [Sharing Text and URLs](#sharing-text-and-urls)
- [Sharing Files](#sharing-files)
- [Clipboard Access](#clipboard-access)
  - [Read Access](#read-access)
  - [Write Access](#write-access)
- [File System Access](#file-system-access)
- [Contact Picker](#contact-picker)
- [Storage](#storage)
  - [Estimate the available storage](#estimate-the-available-storage)
  - [Opt-in to persistant storage](#opt-in-to-persistant-storage)
- [Notifications](#notifications)
  - [Badging](#badging)
  - [Scheduling Notifications](#scheduling-notifications)
- [Wake Lock API](#wake-lock-api)
- [Idle Detection API](#idle-detection-api)
- [Web OTP](#web-otp)
- [Text Fragment](#text-fragment)
- [Periodic Background Sync](#periodic-background-sync)
- [Integration with native app stores](#integration-with-native-app-stores)
- [Devices](#devices)
- [Web NFC](#web-nfc)
  - [Reading NFC tags](#reading-nfc-tags)
  - [Writing NFC tags](#writing-nfc-tags)
- [Content Indexing](#content-indexing)
- [Shape Detection](#shape-detection)
  - [Detecting barcodes](#detecting-barcodes)
  - [Detecting faces](#detecting-faces)
  - [Detecting text](#detecting-text)
- [Transport](#transport)
- [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

The web platform has advanced to an application platform.
With the appearance of HTML5, web applications 
cross-platform 😎TODO

However, there is still a gap between the capabilities of native applications and web apps, the so called app gap.
With the Web Capabilities project, also known as Project Fugu, the Chromium contributors try to close the app gap by evaluating, specifying, and implementing APIs in browsers with native power.

[Progressive Web Apps](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) have
technical foundations such as Service Workers, the Web App Manifest, and IndexedDB. 
Applications can run offline.
However, web applications can only call native functionality
😎TODO

Special emphasis on security and privacy
Most APIs require the website to be sent over a secure connection (HTTPS).
Some of them require a user gesture, such as a click or key press, to prevent fraud (ads).

In this chapter, we'll have a look at different modern web APIs, and the state of web capabilities in 2020. 

## Web Share API

The [Web Share API](https://web.dev/web-share/) allows you to share content with other applications installed on the system using the native share dialog.

## Sharing Text and URLs

```
(🐡 Launched) Web Share (No Files)
```

With Level&nbsp;1 of the Web Share API, web apps can share a title, text, and a URL with other applications.
Chromium forks on Android and Safari currently also support this API.
Using Web Share API is as easy as calling the `share()` method on the `navigator` object.
This method takes an object with a title, text, or a URL to share; at least one option must be given. (TODO: True?)
Next, the native share window will open.
You can await the result of the operation.

```
await navigator.share({ title: 'Test', text: 'Web Almanac 2020' });
```

(TODO: Usage)

## Sharing Files

```
(🐡 Launched) Web Share (Containing Files)
```

Level&nbsp;2 of the Web Share API additionally allows you to share files.
Chromium forks currently support this API on Android.
Safari is adding support with the release of iOS&nbsp;14. (TODO: True?)
Level&nbsp;2 adds a new method called `canShare()` on the `navigator` object.
With the help of this method, you can check if the user agent supports sharing certain content.
Also, you can feature-detect support for Level&nbsp;2 by checking for the existence of the `canShare()` method.
If the content can be shared, you simply add it to the `files` array of the options object, like so: (TODO: more code?)

```
await navigator.share({ files: [myFile1] });
```

(TODO: Usage)

To receive data shared from other applications, web apps can implement the [Web Share Target API](https://web.dev/web-share-target/).
This API allows installed web applications to register with the operating system as a share target.
The web application appears in the native share dialog, and can receive content shared from other apps.

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

## File System Access

```
(🧪 Origin trial) Native File System
```

One of the key features for productivity applications such as IDEs, image editors, or office apps is file system access.
With `input[type=file]` and `a[download]`, the web already has mechanisms to read files from the file system and save them back to the Downloads folder.
The aforementioned applications typically allow you to open single files or a folder, make some changes, and overwrite them in place.
The Native File System API enables you to read and write files from the local file system, open directories and enumerate their contents in a secure and privacy-conserving manner.

To do so, the API introduces a new asynchronous method on the global `window` object called `chooseFileSystemEntries()`.
(😎TODO: this has been replaced by three separate methods https://github.com/WICG/native-file-system/blob/master/changes.md)
This method takes an `options` object that can be used to specify the operation (save/read), kind (file/directory), or file extensions.
After calling this method, the browser will show a dialog box where the user has to select the target file or directory from.
For security reasons, not all directories can be selected, such as system folders or directories containing sensitive data.

(TODO: Analyze data)

The availability of Native File System API could bring a lot of applications and experiences to the web that currently need to be shipped in a native wrapper such as Electron. (TODO: Slack, VS Code, Skype as examples?)

## Contact Picker

```
(🐡 Launched) Contact Picker
```

The [Contact Picker API](https://web.dev/contact-picker/) allows you to access single or multiple contacts from the address book of a user's device.
It does not allow you to request the entire address book or to check numbers against your contacts though.
Comparable to the Native File System API, a picker will appear first, where the user has to select the contact they want to share with the website.

The Contact Picker API introduces a new `ContactManager` interface on the `navigator` object.
Calling `navigator.contacts.select()` returns a promise and opens the contact picker.
This method takes two arguments:
First, an array of contact information the website wants to access, such as `name`, `email`, `tel`, `address`, or `icon`.
As a second argument, the method takes an `options` object which can be used to request multiple contacts by setting the `multiple` property to `true`.
When the user selects contacts and decides to share them with the website, the promise will resolve and return the contact information back to the website.

(TODO: Analyze)

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
This API adds a new property called `showTrigger` to the `options` map that can be passed to the `showNotification()` method on the service worker's registration.
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

## Web OTP

```
(🐡 Launched) Web OTP
```

For verifying a user's phone number or two-factor authentication (TFA or 2FA), services often send a text message containing a one-time password (OTP) to the user's device.
To enter the OTP, users may have to switch to the Messages app, copy the OTP, switch back to the website, and then paste the code into a text field—a tedious task.
To improve this experience, the operating system can share a one-time password with the browser.
This is where the [Web OTP API](https://web.dev/web-otp/) comes in.

To obtain an OTP from a text message, you have to call the `navigator.credentials.get()` method and pass the following configuration object to it: `{ otp: { transport: ['sms'] }}`.
This method returns a promise that will eventually resolve to an `OTPCredential` object containing the OTP.
After that, you can invoke the delivery of the text message on the server.
Make sure to format the text message as follows:
The host part of the website's URL must be preceded by an at sign (`@`), and the OTP must be preceded by a pound sign (`#`), e.g. `@www.example.com #12345`.
You may add additional text to the message for the user.
When the device receives a matching text message, the user has to confirm to share the code with the website.
Now the promise resolves, and you can find the OTP on the `code` property of the resulting object.

(TODO: analyze data)

## Text Fragments

```
(🐡 Launched) Text Fragment
```

URLs are the web's technical measure to link to documents.
With the help of hash anchors, you can even deep-link to certain sections within the document (e.g., `#summary`), if you assigned those sections a matching ID.
However, you could not link to arbitrary passages within a document—until now.
[Text Fragments](https://web.dev/text-fragments/) help readers to identify the relevant parts in a document.
For example, to directly skip to the answer for a specific question.

To do so, you simply add `#:~:text=` to a URL, followed by the text you want to highlight, encoded as a URI component.
Currently, only Chromium-based browsers support this mechanism.
These browsers will find the selected text on the website, and highlight it with a yellow background color.
To highlight a single word, simply append it (e.g., `#:~:text=Start`).
You can also highlight an entire sentence or paragraph by adding the expected end text, separated by a comma (`#:~:text=Start,End`).
Where this isn't enough, you can add a prefix and suffix to narrow down the relevant text even further:
The full syntax for text fragments is `#:~:text=[prefix-,]textStart[,textEnd][,-suffix]`.

(TODO: Image?)
(TODO: Analyze data)

The Google search makes use of this mechanism to guide the user to the relevant part on the website based on the search query.

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

[gIRA](https://web.dev/get-installed-related-apps/)

## Devices

> TODO: These APIs require a user gesture and probably cannot be evaluated.

```
(🐡 Launched) Web USB
(🐡 Launched) Web Bluetooth
(🐡 Launched) Web MIDI
(🧪 Origin trial) Web Serial
```

## Web NFC

[Web NFC](https://web.dev/nfc/)

### Reading NFC tags

```
(🧪 Origin trial) Web NFC (Read)
```

### Writing NFC tags

```
(🧪 Origin trial) Web NFC (Write)
```

## Content Indexing

```
(🧪 Origin trial) Content Indexing
```

The [Content Indexing API](https://web.dev/content-indexing-api/) …

## Shape Detection

The [Shape Detection API](https://web.dev/shape-detection/) allows you to detect barcodes, faces, or text in images.

### Detecting barcodes

```
(🐡 Launched) Shape Detection (Barcode)
```

`BarcodeDetector`

### Detecting faces

```
(🚩 Behind flag) Shape Detection (Face)
```

`FaceDetector`

### Detecting text

```
(🚩 Behind flag) Shape Detection (Text)
```

`TextDetector`

## Transport
```
(🧪 Origin trial) WebSocketStream
(🧪 Origin trial) QuicTransport
```

## Conclusion

The state of web capabilities 2020 is… (TODO)

- security
- privacy
- developer interaction
