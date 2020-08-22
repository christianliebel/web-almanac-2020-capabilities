# Capabilities

The objective of your chapter is to write a data-driven/research-based answer/blog post to this big question: “What is the state of web capabilities in 2020?”

https://github.com/HTTPArchive/almanac.httparchive.org/wiki/Authors'-Guide#writing-the-chapter

https://developers.google.com/style/highlights


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

The web platform as advanced to an application platform.
With the appearance of HTML5, web applications 
cross-platform

However, there is still a gap between the capabilities of native applications and web apps, the so called web app gap.
With the Web Capabilities project also known as Project Fugu, the Chromium contributors try to close the web app gap by evaluating, specifying, and implementing APIs in browsers with native power.

[Progressive Web Apps](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) have
Technical foundations such as Service Workers, the Web App Manifest, and IndexedDB. 
Applications can run offline.
However, web applications can only call native functionality

Special emphasis on security and privacy
Most APIs require the website to be sent over a secure connection (HTTPS).
Some of them require a user gesture, such as a click or key press, to prevent fraud (ads).

In this chapter, we'll have a look at different modern web APIs and the state of web capabilities in 2020. 

## Web Share API

The [Web Share API](https://web.dev/web-share/) allows you to share content with other applications installed on the system.

## Sharing Text and URLs

```
(🐡 Launched) Web Share (No Files)
```

With Level 1 of Web Share API, web apps can share a title, text, and a URL with other applications.
Safari and Chromium forks on Android currently support this API.

## Sharing Files

```
(🐡 Launched) Web Share (Containing Files)
```

Level 2 of Web Share API additionally allows you to share files as well.
Chromium forks currently support this API on Android, Safari is expected to add support with the release of iOS 14.

With the help of Web Share Target API, web applications installed to the system can receive data from other applications.

## Clipboard Access

This is im

### Read Access

```
(🐡 Launched) Async Clipboard Read
```

### Write Access

```
(🐡 Launched) Async Clipboard Write
```


## File System Access

One of the key features for productivity apps such das IDEs or Office-style applications is file system access.
These kind of applications can 

(🧪 Origin trial) Native File System

## Contact Picker

The Contact Picker API allows a secure access to contacts on the system without requesting the entire address book.

(🐡 Launched) Contact Picker

## Storage

The _Storage Estimation API_ allows you to estimate

### Estimate the available storage

(🐡 Launched) Storage Estimation

### Opt-in to persistant storage

```
(🐡 Launched) Persistent Storage
```

Browsers can evict storage when the device is low on disk space.
For instance, Chromium forks on Android remove storage from the least recently used origins.
When dealing with structured data entered by the user, this poses a risk of data loss.
Cache eviction

## Notifications

With the help of the Push and Notifications API, web applications have long been able to receive push messages and display notification banners.
However, some parts are missing:
Up until now, push messages had to be sent via the server; they cannot be scheduled offline.
Also, web applications installed to the system cannot show badges on their icon.
The Badging and Notification Triggers API want to enable both scenarios.

### Badging

```
(🐡 Launched) Badge Set
(🐡 Launched) Badge Clear
```

On several platforms, it's common for applications to present a badge on the application's icon indicating the amount of open actions:
For instance, the badge could show the number of unread emails, notifications or to-do items to complete.
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
Some applications, such as games, calendar or alarm applications, could also determine the target date for a notification locally and schedule it.
To allow this feature, the Chrome team experimented with a new API called [Notification Triggers](https://web.dev/notification-triggers/).
This API adds a new property called `showTrigger` to the `options` map that can be passed to the `showNotification()` method on the service worker's registration.
The API would allow for different kinds of triggers.
For scheduling a notification based on a certain date and time, you can create a new instance of a `TimestampTrigger` and pass the target timestamp to it:

```js
registration.showNotification('Title', {
  body: 'Message',
  showTrigger: new TimestampTrigger(timestamp)
});
```

(TODO: analyze data)

Due to lack of developer interest, the Chrome team decided to pause the further development of Notification Triggers in July 2020.

## Wake Lock API

```
(🐡 Launched) Screen Wake Lock
```

To save energy, mobile devices darken the screen backlight and eventually turn of the device's display, which makes sense in most cases.
There are scenarios however, where the user may want the application to not turn off the display, for instance, when reading a recipe while cooking with dirty hands.

In a [Wake Lock API case study on BettyCrocker.com](https://web.dev/betty-crocker/), the purchase intent indicators increased by about 300 percent. (TODO: More)

## Idle Detection API

```
(🧪 Origin trial) Idle Detection
```

## Web OTP

```
(🐡 Launched) Web OTP
```

For Two-Factor Authentication (TFA or 2FA), services often send a text message to the user's device. 
To improve the experience, the operating system can share the one-time password with the browser, so the user doesn't have to switch apps and copy-paste the OTP.

## Text Fragment

Text fragments help readers to identify the relevant parts 

```
(🐡 Launched) Text Fragment
```

## Periodic Background Sync

When an application was closed, it cannot communicate

In some scenarios, developers still want to synchronize data on a more or less regular basis, for instance, by …

(🐡 Launched) Periodic Background Sync Register
(🐡 Launched) Periodic Background Sync

## Integration with native app stores
(🐡 Launched) Get Installed Related Apps

## Devices

TODO: These APIs require a user gesture and probably cannot be evaluated.

(🐡 Launched) Web USB
(🐡 Launched) Web Bluetooth
(🐡 Launched) Web MIDI
(🧪 Origin trial) Web Serial

## Web NFC

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

## Shape Detection

The Shape Detection API allows you to detect barcodes, faces, or text in images.

### Detecting barcodes

```
(🐡 Launched) Shape Detection (Barcode)
```

### Detecting faces

```
(🚩 Behind flag) Shape Detection (Face)
```

### Detecting text

```
(🚩 Behind flag) Shape Detection (Text)

```

## Transport
```
(🧪 Origin trial) WebSocketStream
(🧪 Origin trial) QuicTransport
```

## Conclusion
