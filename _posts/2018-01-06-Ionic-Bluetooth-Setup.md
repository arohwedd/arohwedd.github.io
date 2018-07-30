---
layout: post
title: How To Setup Bluetooth Low Energy In Ionic
description: A technical step-by-step guide for setting up Bluetooth in Ionic Native
img: Bluetooth-Logo-2.jpg
tags: [Bluetooth, IoT, Javascript, Ionic, Ionic Native]
---

One of the major concerns with using a non-native app development framework (such as Ionic) is how well it will handle using the phone's hardware. Today I'm going to show you how to implement Ionic Native's Bluetooth LE package, and you will see how easy and reliable it is to work with. Let's get to it!

(This article assumes you know how and already have setup an Ionic application. If you still need to learn how to do this, read the docs here https://ionicframework.com/getting-started/.)

Alright, so you have your Ionic application up, and you're ready to use Bluetooth. 

## 1) Download the Bluetooth LE package
```
$ ionic cordova plugin add cordova-plugin-ble-central
$ npm install --save @ionic-native/ble
```

## 2) Import the Bluetooth LE package into your project

All Ionic Native packages such as Bluetooth or GPS are required to be imported in the `@NgModule`'s `providers` property. Most often this file is named something like `app.module.ts`. Head over to yours and enter the following:
```javascript
import { BLE } from '@ionic-native/ble';

...

@NgModule({
 ...
    providers: [
    	 BLE
    ]
```
Of course your code won't look exactly like that, as I took out the other properties inside of `@NgModule` such as `declarations`, `imports`, `bootstrap`, and `entryComponents`.

## 3) Import the BLE package into your component. 

Like adding any package into an Angular component, first import it into the typescript file:
`import { BLE } from '@ionic-native/ble';`
Next, add it to the constructor:

```javascript
  constructor(private ble: BLE) {}
```


Cool! This is all the boring, boilerplate-ish stuff. Now we can actually do some work with BLE. In case it wasn't obvious, you will need an actual Bluetooth device to work with in order to use Bluetooth. I'm using and ITag Keyfinder that I bought on Amazon. The relevant specs for it are:

Device Name: iTag

Service UUID : FFE0

Characteristic UUID: FFE1

You are probably using a different device, and if so, make sure to find out what these specs are for your device. BLE allows a device to have multiple services and characteritistics. For example, if I had a BLE weather device, one of the services would be temperature, another humidity, another wind speed. Point being, there are probably several services and characteristics on your BLE device, and it's important to find out which ones you are trying to use. If you wish to learn more about how this BLE protocol works, there's a lot of reading online (To be honest, I don't know enough about it to write about it myself). 

Alright, back to it!

## 4) Connect your phone to BLE

In order for your device to connect to a bluetooth device, you will have to have an event that initiates the connection. In other words, you can make a "Connect to Bluetooth" button, or something else like that. Let's go with that. So you have a button that says "Connect to Bluetooth". When a user presses the button, a method gets called. Here's the method to put inside your component's class

```javascript
export class ConnectBluetoothPage {
    ...
    connectToBluetooth(): void {
        this.ble.isEnabled().then(() => {
            // the 3 below is how many seconds to scan for. Feel free to make it longer or shorter
            this.ble.scan([], 3).subscribe(device => {
                if (device.name === "iTag") {
                    this.ble.connect(device.id).subscribe(result => {
                        this.ble.startNotification(this.deviceId, "FFE0", "FFE1").subscribe(response => {
                            alert("IT'S WORKING!!!");
                        });
                    })
                }
            });
        })
    }
    ...
}

```

So that code is a little nasty. There's a lot of promises, 4 in fact. There's a lot of ways to structure this code to make it look prettier, but for now I'll just leave it looking like this. Let's talk about what the code does. First of all, let's talk about what it's supposed to do. The iTag device is simply a button. When you click the button, the app will be listening for that click and do something in response. For example, if I wanted to make an app that makes a loud noise every time I click this button, the code above could do that. For now though, All it will do is send a alert message that says "IT'S WORKING!!!" Now I will explain each step

First the method checks to see if Bluetooth is enabled with the `this.ble.isEnabled()` method. If it is, then the app scans nearby bluetooth device. It looks for the bluetooth device with the name "iTag". If it finds a device with that name, it connects to it using the `this.ble.connect(device.id)` method. This `connect` method requires a device Id in order to connect. If it finds a device and connects to it, then we can create a notification service based on the service and characteristic. For my iTag device for the button click the service UUID is `FFE0` and the characteristic UUID is `FFE1`, thus the method is `this.ble.startNotification(this.deviceId, "FFE0", "FFE1")`. This will connect the phone to the iTag button click service, meaning that every time I click the button, the method inside `this.ble.startNotification(this.deviceId, "FFE0", "FFE1").subscribe(response => { alert("IT'S WORKING!!!"); }); ` will run. In other words, the `alert("IT's WORKING!!!");` code will run every time I click the button. Sorry if I'm over-explaining here, but this is the only concept to understand here so I want to make sure you get it. 

What to do next? Well, the rest would be error handling and all of that boring stuff. You could add an error handler to the `connectToBluetooth` such as:

```javascript
connectToBluetooth(): void {
    this.ble.isEnabled().then(() => {
        // the 3 below is how many seconds to scan for. Feel free to make it longer or shorter
        this.ble.scan([], 3).subscribe(device => {
            if (device.name === "iTag") {
                this.ble.connect(device.id).subscribe(result => {
                    this.ble.startNotification(this.deviceId, "FFE0", "FFE1").subscribe(response => {
                        alert("IT'S WORKING!!!");
                    });
                })
            }
        });
        
     // NEW CODE BELOW!!
    }).catch(() => {
      alert("BLUETOOTH IS NOT ENABLED ON YOUR DEVICE PLEASE ENABLE IT");
    });
}
```

Just stuff like that that we all know how to do. I prefer tutorials that only give you the meat of the tool, and not the details such as error handling. I hope you prefer that as well :) 

That's it folks! I hope you learned something. If you didn't, please let me know how I can improve my tutorials. 







