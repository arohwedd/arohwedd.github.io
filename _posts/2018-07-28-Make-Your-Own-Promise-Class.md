---
layout: post
title: Make Your Own Promise Object In Javascript
description: A tutorial on how to make a Promise Object
img: JavaScript-logo.png
tags: ["javascript", "js", "promise", "promises", "callback", "async"]
--- 

Last week I had a question: how do promises actually work in Javascript? How would I build my own? I refused to look at the source code, and wanted to make my own from scratch. So I did (with a few features missing). **My goal of this tutorial is not to show you how to reinvent promises, but the concepts that make my custom promise object possible.** 

I like to think of a promise as more readable callbacks. In my opinion, that is their purpose and reason for existence. And as simple as that is, It's a very good reason to exist. I never want to see callback hell like this again:

```javascript
function(callback){
    $ajaxCall(function(){
        $ajaxCall(function(){
            $ajaxCall(function(){
                console.log("WE MADE IT TO HELL AND BACK!");
            });
        });
    });
}
```

That sucks. It's a huge reason people hate javascript. I don't blame them. Promises look a lot nicer. So let's make our own and see how all the pieces work. 

Here is my simplified `CustomPromise` object. Notice how I only worried about `resolve` and didn't write anything for `reject`. Again, my goal here is to show you some cool concepts behind making this `CustomPromise` object, as the concepts themselves are more interesting than recreating something that already exists. Okay, here it is:

```javascript
function CustomPromise(mainMethod) {
  
  this.thenMethodArr = [];

  setTimeout(()=> {
      mainMethod(resolve.bind(this));
  }, 0)

  this.then = function(callback) {
    this.thenMethodArr.push(callback);
    return this; 
  }

  function resolve(msg) {
    var thenMethod = this.thenMethodArr.shift();

    try{
      return thenMethod(msg).thenMethodArr = this.thenMethodArr;
    } catch(ex){ }
  }

}
```

This is an example of implementing it:

```javascript
var customPromise = new CustomPromise((resolve) => {
  resolve("hello");
});

customPromise.then((message) => {
  console.log("message 1: " + message)

  return new CustomPromise((resolve) => {
    setTimeout(()=> {
      resolve("hello again ");
    }, 3000);
  });
})
.then((message) => {
  console.log("message 2: " + message);

  return new CustomPromise((resolve) => {
    resolve("hello again and again ");
  });
})
.then((message) => {
  console.log("message 3: " + message);
})
```

So at a high level the code does the following in the following order:

1) Uses the `thenMethodArr` array to store (NOT execute) the methods in each `then` method. 

2) Executes the first method (the one in the initial `CustomPromise` call before the `then` methods: `(resolve) => { resolve("hello"); }`

3) Runs `resolve()` for the first time. The resolve method runs the first method in `thenMethodArr`, which **returns** a new promise. Not only does it return a new promise, but that promise already has the `thenMethodArr` populated with the remaining then methods. 

4) From there, it continues to execute new Promises (as each `then` method returns a promise), and when it runs out of promises to return, it completes its execution. 

Ok, there were a lot of concepts in there, so Let me break down each step with the details:

## 1) Uses the `thenMethodArr` array to store (NOT execute) the methods in each `then` method. 

Ok how am I doing this? The first thing the `CustomPromise` object does is run the `then` method:

```javascript
this.then = function(callback) {
  this.thenMethodArr.push(callback);
  return this; 
}
```
Yes, this is the first part of the code that gets run. You may be thinking, why doesn't `mainMethod` run first? That's the method in the first callback, before the `then`s. Well, I cheated a bit. I'm using `setTimeout(()=>{},0)`. By running `mainMethod` inside of `setTimeout` it ensures that the rest of the methods in the method chain run before `mainMethod` does. It gets put to the back of the call stack. `then` is free to populate the `thenMethodArr` array. `return this` is what allows me to method chain, thus I'm able to go through all of the `then` methods and add them to the array. 


## 2) Executes the first method (the one in the initial `CustomPromise` call before the `then` methods: `(resolve) => { resolve("hello"); }`

After the `then` methods have all ran, the original method is available in the callstack. That's this one right here:

```javascript
(resolve) => {
  resolve("hello");
}
```
Easy enough. It gets ran in `CustomPromise` right here:

```javascript
setTimeout(()=> {
    mainMethod(resolve.bind(this));
}, 0)
```

This piece of code is concise, but does a lot. We discussed the `setTimeout`'s purpose already. It also binds the `resolve` method to `this` so that if resolve is used outside of the object (in the implementation) it will know what `this` to use. If that doesn't make sense to you, read more about `bind`. From here, we are able to actually execute the resolve method


## 3) Runs `resolve()` for the first time. The resolve method runs the first method in `thenMethodArr`, which **returns** a new promise. Not only does it return a new promise, but that promise already has the `thenMethodArr` populated with the remaining then methods. 

Let's look at the code 

```javascript
function resolve(msg) {
    var thenMethod = this.thenMethodArr.shift();

    try{
        return thenMethod(msg).thenMethodArr = this.thenMethodArr;
    } catch(ex){ }
}
```
This code works for one important reason: when implementing, each method in `thenMethodArr` besides the last one returns a new `CustomPromise`. Because that is the case, we can return `thenMethod`, and pass the message and also the remaining methods in `thenMethodArr`. Every time we run the resolve method, we are actually returning a new CustomPromise object, and running the CustomPromise code all over again. Because we stacked the `thenMethodArr` prior to running any resolves, we are able to loop through all of them, and catch the last one. Which is step 4:

## 4) From there, it continues to execute new Promises (as each `then` method returns a promise), and when it runs out of promises to return, it completes its execution. 

Okay! To be clear, I have no idea if the Promise object in ES6 actually does it this way. However, this was a great exercise in learning how to write higher order functions, stack methods, and loop through them. And again, all of this can be achieved with callbacks, but the point of Promises is to make callbacks easier to deal with as a human. Oddly enough, I actually prefer `async - await` over promises, but this was a fun exercise. I hope it was helpful to look at this clever piece of code! I know I learned a lot making this tutorial. 