# What's the result?

---

A flawed "bonus". I must admit, I haven't put much effort into this app. So, it's definitely only for the very brave.

---

## Video

<iframe 
    class="video"  
    src="https://www.youtube.com/embed/VpCgMRtabC8" 
    title="MQTT Chat App for SHIMMER/IOTA using Flutter and Rust (iota-client.rs)" 
    frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    allowfullscreen>
</iframe>

That sounds like a real challenge. Yep, I'm talking about the app I used to test out MQTT chat. Clone the app from the repository (see below) if you're feeling adventurous.

I did put together a tutorial chapter, trying to give at least some information and tips how to get it running on macOS and in the iOS Simulator simultaneously. But here's the kicker—I couldn't crack the Android security issue regarding certificates. Sorry, Android fans, you're on your own with this one.

So, what's the verdict? Well, let's just say this app is tailor-made for the daredevils among us, the ones who thrive on a good challenge. If you've got a knack for troubleshooting and a touch of masochism, then by all means, dive right in. But for the rest of us mere mortals? Maybe stick to apps that don't require a crash course in programming just to send a message.

## Github Repository

You will find the complete source code from the video in this repository:

<a href="https://github.com/iota-for-flutter/mqtt_chat_app" target="_blank">👉 &nbsp; Repository - MQTT Chat App</a>

### Usage of the repository

Now here's a hurdle: I'm not exactly a pro at downloading repositories and making them spring to life with just one magic install command (especially when it comes to this complex amalgamation of Rust, Flutter, Flutter-Rust Bridge, Android, and Xcode). Nope, no hand-holding from me on this one. Proceed at your own risk, folks. You've been warned.

My recommendation: Build the app from scratch as you have learned in the previous chapters. Copy the most important code snippets for Flutter and Rust from the MQTT Chat App repository and paste it into your app.

---

When rebuilding an app from scratch, ensure that you take into account all the workarounds, tips, and pitfalls described in the previous chapters. For example:

- A common mistake on iOS is that Pods (dependencies) need to be installed before the initial launch using the additional command "pod install". However, this is automatically done when the application is launched via flutter run.

- On macOS, there might be issues with loading dynamic libraries or forgetting to set the key 'com.apple.security.network.client' to true.
