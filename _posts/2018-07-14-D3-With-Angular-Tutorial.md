---
layout: post
title: Getting Started With D3 And Angular 2+
img: Angular.jpg
--- 

This is a quick tutorial to help you get started on D3 with Angular 2+ (aka "Angular"). This will not go into details of either D3 or Angular, and assumes a basic background in Angular. 

First, let's create an Angular project:

```bash
$ ng new angular-d3-tutorial
```

Now we go straight into installing the D3 package. We will use `d3-ng2-service`:

```bash
$ npm install d3-ng2-service --save
```
Next, setup the boring boilerplate in the module file. Go to `./src/app/app.module.ts`. Towards the top add the import:

```typescript
import { D3Service } from 'd3-ng2-service';
```
In the same file, go down to the providers section and add the following: 
```javascript
providers: [D3Service],
```

The entire file will look like this:
```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

import { D3Service } from 'd3-ng2-service';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [D3Service],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

The boilerplate is done! Hooray. Let's move on to the actual code. 

For simplicity's sake, let's use the `app` component, rather than creating a new one. First open `app.component.html`. Delete everything in that file. replace it with the following:

```html
<div class="chart"></div>
```
This `chart` element will be what our D3 chart goes in. 

Next open the `app.component.ts` file. Import the D3 package at the top:

```typescript
import { D3Service, D3 } from 'd3-ng2-service';
```

For simplicity, we will have our chart be created on the intialization of the component, therefore we will use the interface `OnInit`, which is in the angular core package. Add it to your list of Angular Core imports:

```typescript
import { Component, OnInit } from '@angular/core';
```

then implement the interface:

```typescript
export class AppComponent implements OnInit{
  title = 'app';

  ngOnInit(){
  }

}
```

Cool. `ngOnInit` is the method that `OnInit` implements. We're going to put our D3 method chain in there. Before that, let's setup our D3 objects and constructor. We will have a `D3` object as well as a small list of example data:

```typescript
private d3: D3;
private data = [30, 86, 168, 281, 303, 365];
```

now let's use the constructor to initialize `this.d3`:

```typescript
constructor(d3Service: D3Service){
    this.d3 = d3Service.getD3();
}
```

`d3Service.getD3()` "obtain[s] the d3 object from the D3 Service". Pragmatically, what this means is it initializes the D3 object for us. Awesome. 

Ok let's create a graph. In the `ngOnInit` method, add the following:

```typescript
let d3 = this.d3;

d3.select("div")
    .selectAll(".chart")
    .data(this.data)
    .enter()
    .append("div")
    .style("width", function(d) { return d + "px"; })
    .text(function(d) { return d; });
```

The `app.component.ts` file in its entirety looks like:

```typescript
import { Component, OnInit } from '@angular/core';
import { D3Service, D3 } from 'd3-ng2-service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit{
  title = 'app';

  private d3: D3;
  private data = [30, 86, 168, 281, 303, 365];

  constructor(d3Service: D3Service){
    this.d3 = d3Service.getD3();
  }

  ngOnInit(){

    let d3 = this.d3;

    d3.select("div")
      .selectAll(".chart")
      .data(this.data)
      .enter()
      .append("div")
      .style("width", function(d) { return d + "px"; })
      .text(function(d) { return d; });
  }

}
```

One more step: CSS. Go to the `app.component.css` file and add the following:

```css
.chart div {
    font: 10px sans-serif;
    background-color: steelblue;
    text-align: right;
    padding: 3px;
    margin: 1px;
    color: white;
}
```

Alright, now in the command line run

```bash
$ ng serve
```

Check it out on your browser (by default this would be localhost:4200). It will look bad, like this:

![alt-text](https://c1.staticflickr.com/2/1828/42500799765_c9ed925e3d.jpg "bad graph")

What we want it to look like is this:
![alt-text](https://c1.staticflickr.com/2/1761/28535375327_d47f520cb2_z.jpg "good graph")

What went wrong? Because D3 dynamically generates divs, our new divs actually did not get the styling we created in the `app.component.css` file. Angular's shadow DOM encapsulates style to the DOM elements that exist during initialization. Because these dynamic divs do not exist yet, they will not get styled. There's a few ways to get around this, but the easiest is to change the `app` components `encapsulation` type to `NONE`. To do this, import the `ViewEncapsulation` class in the angular core package:

```typescript
import { Component, OnInit, ViewEncapsulation } from '@angular/core';
```

Next, set the Component's encapsulation property to none:

```typescript
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  encapsulation: ViewEncapsulation.None // This line right here!!!!
})
```

If you kept `ng serve` running, go back to the browser and check to see if the updates worked. You should be seeing a nice blue graph. 

Ok, that wraps it up. Thanks for reading!



