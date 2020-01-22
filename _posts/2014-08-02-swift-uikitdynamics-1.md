---
title: Getting started with UIKit Dynamics in Swift
date: 2014-08-02 00:00:00 Z
permalink: "/blog/2014/08/02/getting-started-uikitdynamics-swift/"
color: "#25c728"
layout: article
---

As you may already know, [Swift](//developer.apple.com/swift/), the new programming language from Apple, has been out for quite a while and almost every iOS developer is today having the itch to make the most out of it.

The first thing you’ll notice in the language is its similarity to most of the dot-notation based languages out there, like JavaScript, ActionScript, Java and so on. This is a massive advantage for people who didn’t have the guts to approach iOS development because of the complexity of Objective-C. Now everyone with a bit of programming experience is ready.

I won’t get into details about the language itself. If you're new to the language I suggest to read the [Swift Cheat Sheet](http://kpbp.github.io/swiftcheatsheet/), or check out [Design\+Code](http://designcode.io/) by [Meng To](https://twitter.com/MengTo), which I strongly recommend, especially for beginners. If you want to dig deeper, you can [download the official E-Book from Apple](https://itunes.apple.com/us/book/swift-programming-language/id881256329?mt=11), free of charge.

In this post I’m going to talk about UIKit Dynamics, a framework used to create complex 2D animations. While we could use the Core Animation framework to perform basic transitions, the new Dynamics framework includes real-world physics behaviors out of the box, like gravity, collision, density and so on. The UIKit Dynamics framework is also very easy to implement, which means you don’t have to be a particle physicist to get started.

So let’s start with a simple idea looking at the real world around us. Imagine you’re standing up holding a box with both your hands perpendicularly to your body. The box has a specific weight and it is made of a specific material. Also there is gravity that is trying to pull it down on the floor, so you’re applying some kind of resistance or force to hold it. If you let the box go, it falls with a certain speed until it hits the floor which is also preventing it to keep falling forever. When the box hits  the ground, chances are it won’t just hit and stop. It might bounce around for a while based on it’s velocity, density or bounciness, or just crash into a million pieces.

This makes sense, but how do we simulate this from within an application? The image below shows the final demo.

![](/assets/images/posts/UIKitGif-1.gif)

Let’s assume you already have Xcode 6 Beta so you can actually write Swift Apps and you already have a UIViewController ready to start with. Now let’s create a box:

<script src="https://gist.github.com/omarfouad/40f587b81fd3635f9173.js"></script>

Go ahead and run this code. You should have a green box sitting in the middle of your iOS simulator. Now let's think. This box shouldn't just float in the air like a ghost, in the real world objects tend to fall (and there are no ghosts). So let's add some gravity behavior to our box.

<script src="https://gist.github.com/omarfouad/d5f3c5dd8f779cc65846.js"></script>

As you see at line 8, we are declaring a `UIDynamicAnimator` which is the backbone of our Dynamic engine. It essentially takes care of each and every behavior we add to our `UIViews`. At line 9 then, we are adding a `UIGravityBehavor` to assign to the box later at line 29. Of course we also instantiated the animator at line 26. However, if you run the app, the box won't fall. This because we didn't add the gravity behavior to our animator yet.

#### Understanding Dynamic Behaviors

There is a very important concept we need to understand before going ahead. You might think that the gravity behavior for instance, should be added to a parent container, and any view inside of that container should undergo the gravity force and thus start falling. That's not correct.

Each Dynamic behavior is assigned to a `UIView` instead. So in our case we are adding it to our `greenBox` instance, which *will be affected by gravity*. But we are not done yet. In the above code we just instantiated the animator and the gravity behavior. Now we need to pass the gravity behavior to the animator, which will animate the view accordingly:

<script src="https://gist.github.com/omarfouad/244cce346ae0aa31965a.js"></script>

Check line 32 from the above snippet, run the app and try to catch the box if you can. But the box keeps falling past the screen boundaries, and that's because there is nothing to contain it. Our *floor* is not there.

![](/assets/images/posts/UIKitGif-2.gif)

#### Adding a collision behavior

With that said, let's add a *Collision Behavior* to our box and make the boundaries of the screen be the boundaries.

Again, in the same way we added the Gravity behavior, but without adding another animator instance:

<script src="https://gist.github.com/omarfouad/2185a51e3517f4f72e49.js"></script>

We declared our collision Behavior at line 10 and instantiated it at line 33. We also set `self.collision!.translatesReferenceBoundsIntoBoundary` to `true`, which will set a collision boundary according to the bounds of the dynamic animator's coordinate system (in our case the boundaries of `self.view`, see line 27).

Now the green box should fall and hit the bottom of the screen, bounce a little bit and stop there. But that's boring isn't it? Let's add some interactivity to the box. We want to be able to grab the box and let it go from different places in our screen, so let's write that.

![](/assets/images/posts/UIKitGif-3.gif)

#### Adding a pan gesture to the box

Let's declare a `UIPanGestureRecognizer` and instantiate it. Then we will add it to our box:

<script src="https://gist.github.com/omarfouad/218f823926486b079879.js"></script>

We instantiated the pan gesture object at line 43 and added it to the `greenBox` at line 44. We also set a target function that will handle the pan gesture. Let's add this function below the `viewDidLoad()` function as follows:

<script src="https://gist.github.com/omarfouad/d9a956f6e1fefc8d0136.js"></script>

If you're not familiar with gesture recognizers, we are now handling any pan gesture that might apply to the greenBox view. Every time you try to drag the box with your finger (or the mouse if you use a simulator), the `panning()` function is called, tracing out a message in the console. We are also creating two variables, `location` and `touchLocation`. The first one stores the location of the dragged point relative to the main view (`self.view`), while the other stores the location of the dragged point relative to the box itself. We will need the latter in a while. For now let's handle our pan gesture properly.

Inside the `panning()` function, we will add three different conditions, each for all three three states of a standard panning gesture.

##### The Began State:

This state is set by the Pan Gesture Recognizer at the beginning of the dragging operation. When this state is the current state of the pan gesture, some setup code will be performed. Let's check line number 8:

<script src="https://gist.github.com/omarfouad/8cc4e10fce1c119e2bb0.js"></script>

In this case, when the state of the pan gesture is equal to `UIGestureRecognizerState.Began` we can match the center of the box to the touch point. The box will be dragged just for a couple of pixels then it will stop. That's because the state of the pan gesture will soon change to `UIGestureRecognizerState.Changed`. So let's handle that.

##### The Changed State:

The Changed state is set by the pan gesture recognizer when the dragging is taking place and thus, the `location` value is changing continuously. We want to set the box location according to the touch location here as well:

<script src="https://gist.github.com/omarfouad/c6badffcce175d0f4758.js"></script>

This is pretty self explanatory. If you run the app, you should be able to drag the box normally by now. When the state of the pan gesture is set to `UIGestureRecognizerState.Changed`, the location of the box is updated repeatedly resulting in a smooth drag. You may have noticed that if you let the box go, it won't fall. That's because the gravity behavior has to be reset in the *Ended state*.

##### The Ended State:

When we release our finger off the box, the state of the pan gesture is set to `UIGestureRecognizerState.Ended`. We will need to add the gravity behavior to the animator so that the box will start falling.

<script src="https://gist.github.com/omarfouad/3319cabfa52944a7fad2.js"></script>

In the code above we also removed all the behaviors attached to the animator (line 12) because of course, we don't want the gravity to pull the box from our fingers when we drag it. In this case we will also have to add the collision behavior again at line 22. Of course we can just remove the gravity behavior alone using `self.animator!.removeBehavior(self.gravity)` but for the purpose of this tutorial, we will leave it just as is. If you test the app now, the box should fall once released, colliding to the screen boundaries.

![](/assets/images/posts/UIKitGif-4.gif)

Now let's make it more interesting. We have added two behaviors to our box. Gravity and collision. Let's add a `UIAttachmentBehavior` to it, so that the dragging becomes more realistic. It will look just like we are pulling around the box instead of just dragging it. However, if you look closely to the box when you first drag it, you will notice that it always moves so that the touch point is always on its center.
That's because we are setting its center as the location of the touch point. To fix that we will need to compensate the touch location by adding an offset, and that is when the `touchLocation` variable comes in handy. We'll get to that in a minute.

#### The Attachment Behavior

We will declare the new behavior and implement it in our `panning()` function:

<script src="https://gist.github.com/omarfouad/faf20d1c6d8c5fb553a3.js"></script>

What's going on in the above snippet is pretty straightforward. I've refactored the code a little but inside of the state conditions. at line 11 we first we remove all the behaviors from the animator. It's important that we remove the behavior inside the `began` state and not above it.

At line 13 we set an `offset` variable which holds the offset to calculate when the box is touched. With this the touch point won't snap to the center of the box but will occur where expected.

At line 14, we instantiate the attachment behavior. The arguments passed are the box, the offset used and the location of the touch point.

At line 15, we finally add the attachment behavior to the animator.
At line 18 we set the anchor point of the attach behavior to the location value of the pan gesture. This way, the anchor point will result exactly at the touch point location inside the green box. Of course we need this to be whenever the pan gesture state is changing, so it updates repeatedly.

At line 21, when the state of the pan gesture ends, we need to remove the attachment behavior, so that the box doesn't remain "attached" somewhere. This way the gravity and collision behaviors will take place.

Now run the app and drag the box around the screen. See how it rotates around the anchor point as we explained above.

How about adding the ability to "throw" the box around instead? By now, whenever you let it go, it just falls down. All we need to do is to add a `UIDynamicItemBehavior`. This behavior is an auxiliary behavior that can be added to UIViews, whenever we want to add extra properties like *linearVelocity* or *elasticity*, and so on. We will declare this behavior when the pan gesture is ending:

<script src="https://gist.github.com/omarfouad/e63cc2df1b521ca7cc1a.js"></script>

In the snippet above we are instantiating a `UIDynamicItemBehavior` at line 23.

At line 24 we are adding a *linear velocity* to the box so that whenever we release our finger, the box follows its original direction. We have to pass to `addLinearVelocity` the velocity value taken from the pan gesture and the box itself. The linear velocity will decrease over time because of the gravity behavior

At line 25, we are adding an angular resistance of 0, which means no angular resistance at all. The higher the value is, the more the box will refrain from rotating. Feel free to play around with this value.

At line 26, we are giving the box some *elasticity* value. The value expected should be a float number from 0.0 to 1.0. 1.0 means the box will be super bouncy. 0.0, means it will bounce very little.

At line 27, we finally add the item behavior to the animator as usual, along with the other ones.

To recap, the final code should look as follows:

<script src="https://gist.github.com/omarfouad/105de8c5369e6f09c80e.js"></script>

#### Conclusion

As you see, we could achieve pretty good results with a few lines of code. The UIKit Dynamics framework, however, shouldn't be used for game development but only to add fancy animations to UIViews in regular iOS applications. If you're more into game development you should use SpriteKit, which performs better in rendering a large amount views using OpenGL. Both frameworks share the same Physics engine though.

You can download the Xcode project from [here](https://dl.dropboxusercontent.com/u/3105794/DynamicSquareApp.zip), which includes a bonus view that collides with the green box. In the next post I will cover the last two behaviors, `UIPushBehavior` and `UISnapBehavior` and of course, don't hesitate to [contact me](https://twitter.com/omarfouad) if you have questions.