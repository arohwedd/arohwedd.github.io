---
layout: post
title: New Principles of Readable Code
date: 2018-01-01
description: My thoughts and methods towards writing more readable code
img: readable-code.jpeg
---

One of the most underdiscussed topics in software development is code readability. Sure it gets brought up from time to time, but seldom do people really take the time to define what code reability is, what it looks like, and what its true value is for software. I'm going to explore this topic today and discuss principles that will immediately improve code readability. 

Most engineers live in a world of black-and-white, right-and-wrong. Logic comes easy to us, and is why most of us chose this profession in the first place. Unfortunately, while being logical is absolutely essential to being a software engineer, being articulate is often an afterthought. It is not that we are incapable of writing code that is easy to read, but perhaps we don't have time or simply haven't prioritized it. 

Readable code is code that *people* can read. Yes that is circular logic. Why am I giving you circular logic? Well, it is painfully apparent that engineers don't seem to worry about other people reading their code. For some reason they do things like shorten words in function names ( `runTheCode()` becomes `rnTeCde()` ). Or have the business logic in the code be a windy string of if-else statements and loops thrown in seemingly random places, without any concern for making it clear what the code is actually doing. Or have classes with multiple abstract purposes, making their use ambiguous and unclear. 

Let me make an analogy: imagine you're handed a recipe that looks like this:

* Cut the tomatoes
* Pour water into the pot
* Put tomatoes in the second pan
* Get Rigatoni noodles
* Turn the burner on hot
* Cut the garlic
* Cut the onions
* Put the pot with water on the hot burner
* Get out the Spinach
* Get a pan and put oil on it (oh there's a first pan!)
* Turn a second burner on
* Put the first pan on this burner
* Put tomatoes on first pan
* Cut Mushrooms
* Put garlic, mushrooms, and onions in the pan with the tomatoes
* After about 15 minutes, put the spinach in this pan
* Before you do that, take the noodles out of the water if they are finished
* Let the spinach soften

Your Pasta and sauce is ready!!

Man that was terrible. This is what A LOT of code looks like, and is why it is so ridiculously painful to read. To be clear, the problem with the recipe above is that the instructions are disorganized and are difficult to understand. It has NOTHING to do with poor design patterns, architecture, scalability, or any of that stuff. No matter what type of design pattern you decide to use, if your logic and/or order of instructions is not organized in a way that makes sense to a human, it will be difficult to read. Sure, you must make the code logical for a computer to process it correctly, but you also need to empathize with your fellow engineers and write code that is easy to understand.

### Alright, I complained a lot. Now let me talk about solutions. 

When I used to teach, I would implement a 3-step method for my lessons
#### 1. Try to create a "need" for the student to learn what I'm teaching. Explain why what I'm teaching is important
#### 2. Provide a high-level overview of the steps to solving this need
#### 3. Provide the details to each step

If you haven't noticed, I am doing this 3-part method to write this post. First I created a need: I told you why code readability is important and tried to convince you that there is too much unreadable code out there. Secondly, I gave you a high-level overview of the steps with the 3 bullet points above. And now, the details of each step! 

## 1. Try to create a "need" for the student to learn what I'm teaching. Explain why what I'm teaching is important
In terms of writing code, how do you write code that "creates a need"? Would that even apply here? Simply put, I do this by writing a summary on the top of the class/file that I am working on. While most people explain what the code does, I start by saying why the code was written. For example, for a `ClientSettings` class, I might write something like `Our clients have custom settings that are unique to the client. We have this custom data in our database and retrieve it into this class.` This explanation not only says what the class does, but also provides context for why it was written. When looking at code that you've never seen before, that simple "why" explanation can go a long way. 

    
----------
----------

## 2. Provide a high-level overview of the steps to solving this need
The first step wasn't actually writing code, but a comment for your code. I kind of cheated there :) . However, providing a high-level overview of the steps will be in the code itself, not a comment. Let's go back to creating a recipe. In fact, let's say you have a robot that cooks this pasta recipe, and that's all it does. If you wrote the code in the order that I have above, would it work? Sure, of course it would. The robot doesn't care what steps the code is in, as long as the logic checks out and you tell it the right instructions. However, humans care about the steps, and that recipe was shitty to read, so let's write these instructions in a way that makes more sense. 

Before I show you a code example, let me go over how to organize these instructions. The two biggest components in this recipe are: 

1. Cooking the Pasta
2. Cooking the Sauce

I'd separate those two functions first.

So with those two big components, let's break them down. First, let's talk about `Cooking the Pasta`:

1. Boil water
2. Cook pasta in water

Next let's break down what `Cooking the Sauce` does.

1. Chopping vegetables (I don't care what anyone says: tomatoes are vegetables!)
2. Preparing the pan
3. Cooking the vegetables in the pan


These are a list of the high-level steps for what to do. Each of these could be its own function (5 functions in total), and you could have the five functions all lined up sequentially, which would be very intuitive to another developer:

```javascript
// Cook Pasta
BoilWater();
CookPastaInWater();

// Make Sauce
ChopVegetables();
PrepareVeggiePan();
CookVegetablesInPan();
```

I might even go as far as splitting these 5 functions into two meta-functions:
```javascript
CookPasta();

MakeSauce();

function CookPasta()
{
    BoilWater();
    PourPastaInWater();
}

function MakeSauce()
{
    ChopVegetables();
    PrepareVeggiePan();
    PutVegetablesInPan(); 
}
```

What's important here is separating the high-level concepts so that it is obvious to the developer what is happening. Do it however you see fit. It's a soft-skill, and it isn't black-and-white, so use your intuition and judgement!

    
----------
----------
## 3. Provide the details to each step 
Finally, let's add details to each step. In other words, this is actually writing what each function does. I'm not going to write the code out, but the logic would be something like this:

1. Cook Pasta
	
	1. Boiling water: pouring water into a pot, putting the pot onto the burner, and setting the burner on high, leaving it on until the water boils. 
	2. Pouring pasta into the boiling water until it is ready, checking it on an interval every 2 minutes

2. Make Sauce
		
	1. Chopping vegetables: Loop through each vegetable and chop it
	2. Preparing the pan: Put oil on pan and let set the burner to medium. Let the oil get a little hot for a couple minutes
	3. Putting the vegetables in the pan and cooking them until complete


-----------------
-----------------




Easy, right? It's so simple, and quite obvious, yet so few engineers actually worry about this kind of thing. You should prioritize making your code easy to read for new developers. In my opinion, it is just as important as writing scalable design patterns or unit testable code. Writing code that scales not only means code that is flexible, but also code that is easy to read and update. If engineers take hours just to figure out what your code is trying to do, isn't that just as bad as engineers spending hours refactoring code that is poorly architected? The good news is writing readable code is quite easy. It baffles me how this isn't a priority in today's software engineering world, but it should be. 

