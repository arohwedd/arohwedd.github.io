---
layout: post
title: Higher Order Components vs. Render Props
img: reactjs.png
--- 

Higher Order Components (HOCs) have become a very common pattern for ReactJS, and is in React's official docs. What are they used for? What problem do they solve? Code reusability. Plain and simple, HOCs are used so code doesn't have to be reused. If you want to learn more about HOCs and how to implement them read [here](https://reactjs.org/docs/higher-order-components.html). 

When I first learned about HOCs, I hated them. Why? One of my biggest priorities as a software developer is code readability, and HOCs make it difficult to understand what the code is actually doing. I have two arguments to support this claim:

1) From looking at inner components that use HOCs, if there are multiple HOCs it is difficult to know which properties are coming from which HOC. Not only that, but this also creates a problem with naming collision: if two HOCs used in a component cannot share the same property name 

2) The design of the code is, in my opinion, *backwards*. Imagine having a car with a steering wheel HOC and the code reads `withSteeringWheel(CarComponent)`. Bad! That looks like we're injecting a car into a steering wheel, when in fact we are doing the opposite. It just makes things hard to read. 

I argue that Render props do not have either of these problems. Not only that, but they also follow the ReactJS Component pattern more organically. Let me show you examples of both an HOC and a render prop. 

My example's criteria: We have a piece of code that is a clock. It's going to be utilized all over our application, thus we only want to write the code once and reference it, instead of copy and pasting it several times. We decide, rather than making a vanilla JS object to display time, we are going to create a clock component that we can reuse throughout the application. 

So one component that needs the `Clock` component is the `Navigation` component. Before I show you what the `Clock` code looks like, let me show you two versions of the `Navigation` code. The first one is one that uses an HOC, the second is one that uses a render prop. To be clear neither of these are HOCs or render props themselves. The first one *implements* and HOC, and the second one implements a render prop

HOC:
```javascript
class Navigation extends Component {
    render() {
        return (
            <div>
                { this.props.currentTime.toLocaleTimeString() } 
                <nav>
                    <a href="">Home</a>
                    <a href="">About</a>
                    <a href="">Shop</a>
                    <a href="">Contact</a>
                </nav>
            </div>
        )
            
    }
}    

var NavigationWithClock = withClock(Navigation)

export default NavigationWithClock;
```

Render Prop:
```javascript
class Navigation extends Component {
    render() {
        return (
            <div>
                <Clock markup={({currentTime}) => (
                    <div> 
                        { currentTime.toLocaleTimeString() }
                    </div>
                )}/>
                <nav>
                    <a href="">Home</a>
                    <a href="">About</a>
                    <a href="">Shop</a>
                    <a href="">Contact</a>
                </nav>
            </div>
        )
    }
}

export default Navigation;
```

So, at first glance, the HOC one looks a lot cleaner. This `<Clock>` component is kind of ugly in there. But wait, I'm supposed to hate HOCs, right? Why do I prefer the component that implements a Render Prop? Because I can see where the `currentTime` variable is coming from. In the render prop version, it is obvious `currentTime` is coming from the `<Clock>` component. The version that uses HOC just has this magic `currentTime` property that has no obvious source. In fact, it is possible and very likely that another developer will see this code, and have no idea where this `currentTime` variable comes from. You'd probably have to leave a comment so another developer will know where the source is:

```javascript
class Navigation extends Component {

    // currentTime comes from the withClock HOC. This component depends on withClock
    render() {
        return (
            <div>
                { this.props.currentTime.toLocaleTimeString() } 
                <nav>
                    <a href="">Home</a>
                    <a href="">About</a>
                    <a href="">Shop</a>
                    <a href="">Contact</a>
                </nav>
            </div>
        )
            
    }
}    

export default Navigation;
```

Already we have code that is not obvious to the reader, and we have to write a comment in order to explain what's happening. In my opinion, if you need to comment, you have bad code. If you have multiple required HOCs, it makes things even more tedious to figure out. Which property comes from which HOC? Imagine if there were 10 props and you had to track down which HOCs it came from! That would be terribly hard to keep track of. Hopefully the developer that did this left a comment! While the Render Prop version is a little uglier, it is almost impossible to make the mistake of improper implementation. It is quite clear where the props are coming from.

Point #2: The implementation of the `Clock` component feels backwards. This navigation component is using the Clock component inside of it. Intuitively, any sane person would imagine the `Clock` component inside of the `Navigation` component. Unfortunately HOCs read the opposite of this: `withClock(Navigation)`. I mean, it reads the way Yoda talks: "with clock, navigation." Yoda's great, but writing code like that is confusing. If you look at the actual `withClock` method, It reads as if you are injecting any Component into the `withClock` component:

```javascript
function ClockHoc (WrappedComponent) {

    return class Timer extends Component {

        constructor(props){
            super(props);
            this.state = { 
                currentTime: new Date()
            }
        }

        componentDidMount(){
            this.timer = setInterval(
                () => { this.tick() },
                1000
            )
        }

        tick() {
            this.setState({
                currentTime :  new Date()
            }) 
        }

        componentWillUnmount(){
            clearInterval(this.timer);
        }

        render() {
            return ( 
                // THIS RIGHT HERE!!!!!
                <WrappedComponent currentTime={this.state.currentTime} {...this.props } ></WrappedComponent>
            )
        }
    }
}
```

If you see the `jsx` in the `render()` method, you will see that the `WrappedComponent` is actually inside of the HOC. This is backwards! I can't stress how confusing this is! Look at the render prop version. It simply passes the state over to whoever injects it:

```javascript
class Clock extends Component {
    static propTypes = {
        markup: PropTypes.func.isRequired
    }

    constructor(props){
        super(props);
        this.state = { 
            currentTime: new Date()
        }
    }

    componentDidMount(){
        this.timer = setInterval(
            () => { this.tick() },
            1000
        )
    }

    tick() {
        this.setState({
            currentTime :  new Date()
        }) 
    }

    componentWillUnmount(){
        clearInterval(this.timer);
    }

    render() {
        // This makes more sense!
        return this.props.markup(this.state)
    }
}
```

This component has no markup in it. And that's how we want it! This clock is simply a property that gives us the time. Notice too how I can leverage PropTypes to require the `markup` property to be a function. If someone else uses the `Clock` component and forgets to add that `markup` property, the compiler will tell them forgot it and that it needs to be a function! That's great. That's readable, understandable code. 

As code bases get larger, I argue that scalable architecture is only scalable if other developers can read it. If you're a UI architect and leave your company, will the developers you leave behind be spending hours just trying to figure out how your code works, or will they be writing new code? In my opinion, render props make code way easier to understand than HOCs. 