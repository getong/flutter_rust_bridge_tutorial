# Overview

---

A Small App for Sending and Receiving Messages.

---

## Use Case

As shown in the video, the aim of the app is to allow individuals to exchange messages while they are simultaneously utilizing the same channel of a selected network. Users can choose their own names.

In the app, there are currently two network nodes selectable, one in the Shimmer Testnet and the other in the Shimmer Network. If you operate your own node, you can easily redirect the message transmission of the app through your node by simply exchanging the URLs.

The term "channel" or "tag" is synonymous with a chatroom. The choice of the channel/tag name is entirely arbitrary. For clarity, I've opted to prepend a "#" symbol before the name.

The app does not store messages broadcasted by the channel/tag.

### From a Technical Perspective

The idea is for the app to register with a network node using the inx-mqtt extension. INX-MQTT extends the node endpoints to provide an Event API for listening to live changes occurring in the Tangle.

This API is by default reachable using MQTT over WebSockets.

To test this, the app must be opened simultaneously on at least two devices. For this purpose, I've chosen the iOS Simulator on one end and the macOS app on the other. Further down I'll describe how to launch both apps simultaneously.

### Known Bugs

Apart from known long transmission times, which for reasons unknown to me can range from a few seconds (acceptable) to a few minutes, there are a few errors that I have not investigated.

- Messages are sometimes sent multiple times. This may be due to faulty registration or deregistration in the Rust backend.
- When switching networks, settings are not cleanly saved and updated. This is a problem on the Flutter side.
- After changing channels or networks, there are occasional error messages.

## Basic Building Blocks

There are few basic building blocks worth highlighting.

### Flutter Chat UI

<a href="https://pub.dev/packages/flutter_chat_ui" target="_blank">👉 &nbsp; Flutter Chat UI Plugin</a>

This plugin serves as the heart of the user interface, handling a significant portion of the programming for message display and transmission functionalities. It's configurable, and I've set it up to only transmit text messages. Essentially, the programming effort boils down to registering the app with the node, enabling message publishing, and populating the message stream with messages from the Rust backend.

### Shared Preferences

<a href="https://pub.dev/packages/shared_preferences" target="_blank">👉 &nbsp; Shared Preferences Plugin</a>

This plugin is utilized for reading and writing simple key-value pairs in the frontend. It wraps NSUserDefaults on iOS and SharedPreferences on Android. The app stores the last chosen settings using this mechanism.

### MQTT Client in Rust Backend

I wanted to encapsulate the MQTT functionalities into a separate file. Therefore, on the Rust side, there is the module `mqtt`, which contains an MQTT client with functions for initializing, opening, and closing a channel, publishing a message, as well as receiving messages from the MQTT extension.

When opening a channel, it is implemented that incoming messages are further processed through `log.info!` by the Rust logger within the backend. The uniqueness of the logger is described in the next section.

### Logging in Rust Backend

At this point, I refer to the chapter [Logging Example App]('../building-without-iota/flutter-and-rust/logging-example-app/README.md'). Here, a simple mechanism is described for forwarding Rust logging messages to the Flutter frontend in the form of a stream.

We leverage this approach here.

The `logger` module in the MQTT Chat App is structured similarly to the one described in the mentioned chapter. With one exception. Here, I've incorporated a switch that allows differentiation based on syntax or special delimiters '@@@' in the message string, indicating whether it's a "normal" log info or a log info containing an MQTT message.

Admittedly, this is programmed rather poorly. But my focus here was on a quick solution that allows the entire process from A to Z to be tested in the simplest way possible.

## Launching two apps in your working environment

Here's my approach to starting:

In VSCode, I first launch the iOS Simulator using `open -a Simulator`. Then, I launch the app using `flutter run`. This automatically executes "pod install" if necessary.

Next, I start the second instance of the application from Xcode. I use the Runner.xcworkspace in the macOS directory for this purpose.
