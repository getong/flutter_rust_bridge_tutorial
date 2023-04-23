# ☕ When you are JS developer

---

Flutter vs. JS Frameworks

---

Hold on to your keyboards because it's time for a chapter that will be more exciting than a cat chasing a laser pointer. We're about to explore how familiar Flutter looks when you compare it to JavaScript frameworks and what other reasons there are top use it - and let me tell you, it's going to be so much fun you might want to put on a party hat.

Well, I know some of you might be thinking, “Wait a minute, I'm a JavaScript developer, why should I care about Flutter?” Well, let me ask you, have you ever heard the phrase “Diversity is the spice of life " heard? Yes, that also applies here. By expanding your skills and learning more about Flutter, you might discover a whole new world of possibilities and solutions to your coding challenges.

So get ready to rock and roll, code warriors, because it's time to expand your programming horizons and discover why Flutter should play a part in your life.

> When I first started learning Flutter, I had zero prior knowledge. To give myself a boost of motivation, I turned to videos to get a better understanding of what Flutter is all about. Watching videos not only helped me to get started with Flutter, but also gave me the confidence to continue learning and building my own apps.

## Fun factor

Google and others provide tools and tons of material that make working with Flutter fun. Here are two examples.

1. The YouTube Channel of _Mitch Koko_ - he creates stunning user interfaces:

   <a href="https://www.youtube.com/@createdbykoko" target="_blank">👉 &nbsp; 📱I create apps & tutorials about creating apps👨🏽‍💻</a>

2. The official Flutter YouTube Channel - you'll find amongst others:

   <a href="https://www.youtube.com/playlist?list=PLjxrf2q8roU23XGwz3Km7sQZFTdB996iG" target="_blank">👉 &nbsp; Short videos about the weekly widgets or packages</a>

Flutter is like a treasure trove of creative possibilities, just waiting for you to unlock its secrets. With Flutter, you can build stunning user interfaces, craft intricate animations and interactions, and even dip your toes into the wild and wacky world of game development. You'll discover lots of sources of inspiration that will leave you buzzing with excitement.&#x20;

Later on, you will realize how interacting with Rust libraries empowers you to develop potent products - precisely what you are aiming for.

## But - everything as code!?

In Flutter, a component is a self-contained, reusable piece of a user interface. It is a widget that can be used in a Flutter app to create a specific visual and/or interactive element, such as a button, text field, or image. A component in Flutter can be composed of other components, making it possible to create complex UIs by assembling smaller, reusable parts.

Doesn't that sound familiar !?&#x20;

Let's compare a single Flutter widget with a Vue.js component.

```
import 'package:flutter/material.dart';

class MyButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: () {
        print('Button clicked');
      },
      child: Text(
        'Klick mich!',
        style: TextStyle(color: Colors.white),
      ),
      style: ButtonStyle(
        backgroundColor: MaterialStateProperty.all<Color>(Colors.green),
        shape: MaterialStateProperty.all<RoundedRectangleBorder>(
          RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(20),
          ),
        ),
      ),
    );
  }
}
```

In Vue.js it looks like:

```
<template>
  <button @click="handleClick" class="my-button">Klick mich!</button>
</template>

<script>
export default {
  methods: {
    handleClick() {
      console.log('Button clicked');
    }
  }
}
</script>

<style>
.my-button {
  background-color: green;
  color: white;
  padding: 10px;
  border: none;
  border-radius: 20px;
  cursor: pointer;
}
</style>
```

Yes, yes, yes, I hear some of you saying that everything as code isn't nice.&#x20;

### So where is the benefit?

Flutter's approach of writing everything as code provides a more streamlined and cohesive development experience compared to the traditional separation of HTML, CSS, and JS. With Flutter, you can create custom user interfaces and animations with greater ease and control, and the resulting apps are faster and smaller. Plus, no more switching between different languages and files like a mad scientist!

A very important difference to JavaScript Frameworks is: Flutter compiles the Dart code to native machine code. Theoretically you have the control over every pixel on the screen. BTW, this also differs from React Native where written code is compiled only to the native UI components.
