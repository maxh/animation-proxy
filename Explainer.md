JavaScript-driven accelerated animations
===============
{vollick, jbroman, ajuma, rjkroege}@chromium.org

Problem
-------
JavaScript-driven animations are extremely flexible and powerful, but are subject to main thread jank. And although JavaScript can run on a web worker, DOM access and css property animations are not permitted. Despite this limitation, main thread animations are widely used; they're the only way to create common effects such as parallax, position-sticky, image carousels, custom scroll animations, iphone-style contact lists, and physics-based animations. The result is needlessly janky pages.

Why do we have this limitation? Updating css properties _could_ effect a style recalc or a layout, those operations must happen on the main thread. That said, there are certain 'layout-free' properties that can be modified without these side effects. These properties include transform, opacity and scroll offset. Clearly identifying these layout-free properties, and allowing them to run from any thread, would provide a simple and powerful way to fix these janky pages.

Goal
----
To allow authors to create accelerated animations of css properties via JavaScript. At first we will restrict ourselves to accelerating animations of transform, opacity and scroll offset since virtually all UAs already support accelerated animation of these properties, either by accelerated css animations or threaded scrolling.

High Level Proposal
-------------------
Allow the creation of a proxy to layout-free properties. This proxy could then be used (asynchronously) on web workers. It will also be convenient to allow requestAnimationFrame callbacks to be executed on web worker threads.

A Tiny, But Surprisingly Expressive Kernel
------------------------------------------
What does this proxy buy us? Well, a number of proposals that have been drafted to address some of the use cases mentioned in the problem statement could largely be implemented as polyfills on top of this kernel. So, too, could some existing bits of the API. Specifically,

 - [Web Animations](http://dev.w3.org/fxtf/web-animations/)
 - [Touch-based Animation Scrubbing](https://docs.google.com/document/d/1vRUo_g1il-evZs975eNzGPOuJS7H5UBxs-iZmXHux48/edit)
 - [position:sticky](http://updates.html5rocks.com/2012/08/Stick-your-landings-position-sticky-lands-in-WebKit)
 - [Smooth Scrolling](http://dev.w3.org/csswg/cssom-view/), Sections 4, 5, 7, 12, and 13.
 - Accelerated CSS animations. ([I/O talk](http://www.youtube.com/watch?v=hAzhayTnhEI))

It’s clear from the number of attempts at solving the smooth animation problem that this is extremely important for the web, and it would be wonderful if this problem could be addressed with a small, easy-to-implement kernel. It would also let creative web developers create new animation authoring libraries that are just as performant as their native counterparts. In addition to its simplicity, another benefit of this approach is that it ‘explains the web’ in the Extensible Web Manifesto sense. In particular, it explains accelerated animations. This brings us to...

The Big Concern
---------------
We want to explain the web, not the particular implementation details of a particular browser at a particular point in its history. Are we marrying ourselves to implementation details here?

No! Virtually all user agents support, via css, accelerated opacity and transform animations. And they’re going to have to support them for the foreseeable future. By whatever means these browsers are able to guarantee that things can slide around and fade in and out efficiently for css animations, they will be able to permit these effects to be driven by JavaScript. It doesn’t, for example, tie us to the idea of a composited layer or a layer tree, concepts that may not even exist in all browser implementations. The animated DOM elements might, say, be redrawn by the GPU each frame. But this doesn’t matter. These implementation details are orthogonal to proxy concept.

What About The Details?
-----------------------
This explainer was kept intentionally high level. Our hope is to convince you of the merit of this problem and idea before diving into a debate about our particular solution. We, of course, have some idea of what the proxy API might look like, how you might create them and how we could address timing issues, but that conversation will come later. We look forward to having it with you!

...well, maybe it wouldn't hurt to give a few examples.

TODO(vollick) add examples here.