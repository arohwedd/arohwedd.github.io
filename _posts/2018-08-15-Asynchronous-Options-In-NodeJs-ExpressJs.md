---
layout: post
title: Callbacks, Promises, and Async/Await in NodeJS and Express
description: A tutorial on how the different asynchronous options in Node and Express
img: js-logo.png
tags: ["javascript", "js", "promise", "promises", "callback", "async", "await"]
--- 

This is a general guide to the three main options in Node for asynchronous programming: callback, promises, and async/await. You can download the source code [here](https://github.com/arohwedd/async-node-tutorial). 

Basically, all the code is doing is showing all the documents in the `people` collection in a Mongo database.

## Callback Style

#### Model:
```javascript
var showAllCallback = function(callback) {
    
    var people = [];

    mongoClient.connect(function(err, client) {
        
        var db = client.db("async_tutorial");
    
        db.collection("people").find().toArray(function(err, arr) {
            people = arr;

            mongoClient.close();

            callback(people);
        });
    });
}
```

Notice the `callback` method that gets called in the inner-most part of the method. `showAllCallback` will run all of its code, then run whatever the `callback` method is. Below is an example of that:

#### Controller:
```javascript
var getAllCallback = (req, res) => {
    People.showAllCallback(function(people){
        res.send(people);
    });
}
```
You can think of whole point of the `showAllCallback` method as a tool that populates the `people` parameter, which can be used in the callback. This can get ugly if you need to do multiple callbacks. This is known as callback hell:

```javascript
var getAllCallback = (req, res) => {
    People.showAllCallback(function(people){
        console.log("first time");
        console.log(people);

        People.showAllCallback(function(people){
            console.log("second time");
            console.log(people);

            People.showAllCallback(function(people){
                res.send(people);
            });
        });
    });
}
```

This works, but it's pretty terrible to look at and keep track of. 

## Promise Style

#### Model
```javascript
var showAllPromise = function(){
    return new Promise(function(resolve, reject) {
        mongoClient.connect(function(err, client) {
            var db = client.db("async_tutorial");
            
            db.collection("people").find().toArray(function(err, people) {

                mongoClient.close();
                return resolve(people);
            });
        })
    });
}
```
This returns a `Promise`. A fun way to think of promises is to think of them as `thenables`. A thenable is simply a method that you can chain `then` to. Some people, including myself, find their implementation easier to read than callbacks. Example of an implementation below:

### Controller
```javascript
var getAllPromise = (req, res) => {
    People.showAllPromise()
    .then(function(people) {
        res.send(people)
    });
}
```
If you wanted to do more complex asynchronous code, you could chain them like this:

```javascript
var getAllPromise = (req, res) => {
    People.showAllPromise()
    .then(function(people) {
        console.log("FIRST TIME");
        console.log(people);
        return People.showAllPromise()
    })
    .then(function(people) {
        console.log("SECOND TIME");
        console.log(people);
        return People.showAllPromise()
    })
     .then(function(people) {
        res.send(people)
    })
}
```

This is a lot easier to follow. My favorite is what's next:

## Async/Await
 
 Async/Await's model is actually the same as the Promise's model. In fact, async/await requires a promise in order for it to do its magic:

#### Model
```javascript
var showAllAsync = function(){
    return new Promise(function(resolve, reject) {
        mongoClient.connect(function(err, client) {
            var db = client.db("asnychronous_tutorial");
            
            db.collection("people").find().toArray(function(err, people) {

                mongoClient.close();
                return resolve(people);
            });
        })
    });
}
```

However, its implementation is a bit different. Notice the `async` and `await` keywords. Try to find them (It's not hard lol).

```javascript
var getAllAsync = async (req, res) => {
    res.send(await People.showAllAsync());
}
```

And here's an example of `async/await` chained:

```javascript
var getAllAsync = async (req, res) => {

    var people = await People.showAllAsync();
    console.log("first time");
    console.log(people);

    var people2 = await People.showAllAsync();
    console.log("second time");
    console.log(people2);

    res.send(await People.showAllAsync());
}
```

This one is the least verbose and reads like a synchronous program would. I like it, but it's up to you! That's all. See ya later!


