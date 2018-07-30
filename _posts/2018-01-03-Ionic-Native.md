---
layout: post
title: I Recommend Ionic Native For Almost Any App Development Project. Here's Why.
description: A high-level easy read about my experience with Ionic and Ionic Native
img: ionic.jpeg
tags: [Ionic, Ionic Native, Angular, Javascript, IoT]
---

For many years, I was against the idea of Javascript mobile app frameworks such as React Native and Ionic. Even though I personally love Javascript, I firmly believed that apps needed to be built in the language and environment that they were designed for, Java for Android, and Objective-C/Swift for iOS. I did research on this, found a lot of people who argued against things like Ionic or Xamarin, and convinced myself that they were right. This is what I believed for many years. 

Recently, I was somewhat obligated to use Ionic. I had taken some contract work, and their mockup/prototype was built in Ionic, and it would have been way too much work to rewrite the whole thing in Android and iOS. So, against my beliefs, I went forward with their app using Ionic. Although I was against doing this, I am a Javascript junkie and use Angular almost daily (Ionic heavily leverages Angular), so it was quite easy for me to figure out how Ionic works. Next thing I know, the app is done, it works great, and we have it in the app store. I think to myself "wait a second, this is supposed to be a terrible app! It's not using the native languages!" And yet in front of me is a great app that I built very quickly.

I started to investigate the typical fears of building a framework app: performance and unfixable bugs. Here's the thing about performance: I didn't even bother doing any benchmark testing because the app ran just as well as any other app I've ever used. It felt great, so it was silly to worry about these numbers. As for unfixable bugs: first of all, I think this unfixable bug myth about these frameworks is due to relying on code that you can't control. Hypothetically, I could be 20 weeks into building an app, there could be a bug with the framework itself, and I would be absolutely unable to fix it without trying to fix the framework. That would be really bad, and I could see how the fear of that alone would turn people off. Fortunately, I have not had this issue whatsoever, and looking online, I don't think many people have. Ionic is actively being worked on and has been for about 5 years. For me, that's pretty solid evidence that Ionic is sturdy and stable. 

Before I wrap things up, let me talk about Ionic Native. Ionic Native is a library of open-source plugins that allow you to use Ionic to do things like use Bluetooth, GPS, background services, vibration. Ionic calls these "native" features; I like to call them hardware features. Whatever you want to call them is fine by me :) . Anyways, Ionic Native is what has made me especially impressed with the Ionic framework, and is why I will probably never go back to developing in Java or Swift. Recently, I built an app called [SafeSwitch](https://safeswitch.me) using Ionic Native. It uses Bluetooth LE, GPS, and background services, and I didn't have to touch and Java or Objective-C/Swift. Until recently you couldn't do things like this using Ionic or React Native, thus were obligated to use the truly native mobile app environments. Ionic (and I believe React Native) now have this option, and I very much recommend it. It's fast, easy, and pretty reliable. I will say that Ionic Native did give me a few headaches. Some of the tools it has on there (such as BackgroundGeolocation), had some bugs in it that I had to research and fix myself. Fortunately they were known bugs, and fixes were already provided on their github repos.

Long story short, Let me give you a listicle review of Ionic Native

1. You can develop for both iOS and Android with one codebase, essentially cutting your development time in half
2. It heavily leverages the Angular framework, thus if you have experience in Angular, using Ionic will be a breeze
3. It is stable: I have built two apps now using Ionic and have yet to have any major issues
4. It is being actively maintained and will continue to improve
5. Ionic Native allows you to use the phones hardware, which gives you all the more reason to stay away from building apps the old-fashioned way
6. Ionic Native is a collection of open-source plugins for Ionic, and while they are mostly fantastic, I have found a few bugs that took awhile to resolve. I have yet to find an unfixable bug with any of these plugins, and all of the fixes I have found were only a line or two of code

I am now a huge advocate of Ionic Native. It's more efficient, and you can code in a framework I love: Angular. In hindsight, frameworks such as Ionic are the inevitable evolution of mobile app development. Let's face it: coding one app in two environments really sucks, and it only makes sense to make a solution that enables you to use just one codebase. I think that solution is Ionic.

If there are any questions or concerns you may be having about Ionic, shoot me an email at arohwedder27@gmail.com and I'll answer them. New technologies are risky, and you should always do your research before committing to one. 
