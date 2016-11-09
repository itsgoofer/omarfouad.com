---
title: Getting started with UIKit Dynamics in Swift - Part II
date: 2014-08-06 00:00:00 Z
permalink: "/blog/2014/08/06/getting-started-uikitdynamics-swift-2/"
layout: article
---

In the [previous post](//omarfouad.com/blog/2014/08/02/getting-started-uikitdynamics-swift/), I wrote about UIKit Dynamics covering the following behaviors:

1. UIAttachBehavior
2. UICollisionBehavior
3. UIGravityBehavior

This time I'm going to talk about the last two ones, `UISnapBehavior` and `UIPushBehavior`.
These new behaviors are implemented in the same way we implemented the behaviors in the list above, however they will behave differently as their name implies. 

As usual, let's assume you have xCode 6 Beta (any beta version) installed, with an empty View Controller set up. Then let's add a `greenBox`, a `UIDynamicAnimator` and a `UICollisionBehavior` just in case:

<script src="//gist.github.com/omarfouad/a4be0d3b5d10d47c9663.js"></script>

We will start with the push behavior. Note at line 18, for the purposes of this tutorial, we have added a transform to our greenBox to rotate it a little bit (45 degrees). Now let's declare a push behavior and instantiate it. Then add it to our dynamic animator:

<script src="//gist.github.com/omarfouad/08e69d0cdc6f5b9acc15.js"></script>

#### The Push Behavior:

The push behavior is very simple. Basically, the UIView that receives the push, is "pushed" away in a specific direction and speed, based on the push settings. In the real world, an object that is pushed, would stop because of gravity, friction and other physical factors. In the space though, if you push an object, it will travel forever in that trajectory, until it finds some meteor to smash into. But that's another story.

It's important to know that this behavior has two modes: `UIPushBehaviorMode.Instantaneous` and `UIPushBehaviorMode.Continuous`. The Instantaneous mode makes the UIView receive only one push per time. That means that after some time, the movement of the object slows down until it stops (unless you give a high elasticity to the object for example, at that point it will bounce around forever). 

On the other hand, the Continuous mode, will push the object repeatedly, adding to each time more speed to it gradually. This would be ideal for rockets, cars and space ships. If you do game development this could be useful. 

In the code above, the push mode is Instantaneous, so the box is pushed upwards, colliding to the edge of the screen and bouncing back. Note that we don't have any gravity set up in this example. At line 30 we have are adding both an angle of -180 degrees to the push (upward direction) and a magnitude value, which is the "strength" of the push. Build and see:


![](/assets/images/posts/uikitdynamics2-1.gif)


If you change the push mode to `UIPushBehaviorMode.Continuous`, the box will hit the edge of the screen bounce a little and still move upwards, sticking to the boundary trying to get through it, of course, the poor little green guy, will never get out of there: 


![](/assets/images/posts/uikitdynamics2-2.gif)


Let's make it a bit more interesting, by pushing the green box only when tapping on it. For that we will need to add a simple `UITapGestureRecognizer` and handle it. Follow along: 

<script src="https://gist.github.com/omarfouad/f2d1e916bc204a89661d.js"></script>

We are handling the tap gesture into the `onTap()` function and, at line 44, we are adding a new direction and magnitude force to the push behavior (which is already attached to the green box instance). This time, instead of removing and re-adding the behaviors to the animator, we just deactivate and activate back the push behavior to renew it and thus make it work every time the green box is tapped:

![](/assets/images/posts/uikitdynamics2-3.gif)

As you see in the image above, we are literally dribbling the box making it bounce on the ceiling of our screen waiting for it to come down to re-push it back. Pretty neat he? You can even call `setTargetOffsetFromCenter` which takes the an offset value which is the location of the push. If you push the object from a corner, the object will literally rotate from there accordingly. See the [relating Apple documentation](https://developer.apple.com/library/ios/documentation/uikit/reference/UIPushBehavior_Class/Reference/Reference.html#//apple_ref/occ/instm/UIPushBehavior/targetOffsetFromCenterForItem:) for more info.

#### The Snap Behavior:

The Snap behavior, or `UISnapBehavior` is very straightforward. All it does is telling the object to snap back to some position, usually an original position. Let's set up some code for this example. We will declare the snap behavior, instantiate it giving it the location to snap to (in this case the center of the screen), and we will add a `UIPanGestureRecognizer`. If you are not familiar with pan gestures, please I recommend reading my previous post which covers it in details. From there will add the snap behavior to the animator whenever the pan is ended or on `UIGestureRecognizerState.Ended`. This way when the green box is released, it will snap back to its original location with a cool, elastic transition. Check out the complete code code:

<script src="https://gist.github.com/omarfouad/692f8ac36c352bf9e5af.js"></script>

At line 58 we are adding the snap behavior to the animator. We also have to remove it every time we try to drag or push (by tapping) the green box, thus we did at line 46 and 65. The sequence of removing and re-adding behaviors, in combination with the Push's `active` property is very important to make things work together. 

![](/assets/images/posts/uikitdynamics2-4.gif)

Now that you know the mechanics of UIKit Dynamics I strongly recommend to check the [API documentation here](https://developer.apple.com/library/ios/documentation/uikit/reference/UIDynamicAnimator_Class/Reference/Reference.html#//apple_ref/doc/uid/TP40013153) and have fun creating crazy animations in your next apps!

Here is the [Xcode 6 project](https://dl.dropboxusercontent.com/u/3105794/DynamicSquareApp2.zip) and as usual, feel free to [contact me on Twitter](https://twitter.com/omarfouad) if you have questions.