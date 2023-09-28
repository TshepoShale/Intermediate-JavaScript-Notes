# Intermediate-JavaScript-Notes
# Week 1: 
/These will be my notes covered for intermediate JavaScript. 
# Table of Contents: 
* Same Origin Policy
* window.onscroll Event
* Resizing windows and scrolling


# Day 1:
# The “Same Origin” policy states that:

If we have a reference to another window, e.g. a popup created by window.open or a window inside <iframe>, and that window comes from the same origin, then we have full access to that window. Otherwise, if it comes from another origin, then we can’t access the content of that window: variables, document, anything. The only exception is location: we can change it (thus redirecting the user). But we cannot read the location (so we can’t see where the user is now, no information leak).


# window.onscroll Event: 

The `window.onscroll` event in JavaScript is an event that gets triggered when a web page is scrolled up or down. Let me explain it to you in simple terms:

Imagine you're reading a really long webpage, like a story or an article, and you use your computer's mouse or your touchscreen device to scroll through the content. As you scroll down, new parts of the webpage become visible, and the stuff you've already read moves up and disappears from view, right?

Well, the `window.onscroll` event is like a way for your computer to tell the web page, "Hey, someone is scrolling up or down on this page!" It lets the web page know that the user is interacting with it by scrolling.

So, when you see or hear about the `window.onscroll` event in JavaScript, it means that you can write some code that responds to this event. For example, you could use it to make things happen when the user scrolls. Maybe you want to change the color of a button, show a hidden menu, or do something fancy with the content on the page. It's a way to make your webpage more interactive and responsive to what the user is doing.

Here's a simple code example:

```javascript
window.onscroll = function() {
  // This function will be called whenever the user scrolls on the webpage
  console.log("You're scrolling!");
};
```

In this code, whenever the user scrolls, the message "You're scrolling!" will be shown in the web browser's console. You can replace `console.log("You're scrolling!");` with any JavaScript code you want to run when the user scrolls.

So, in a nutshell, the `window.onscroll` event helps you create cool and interactive effects on web pages when people scroll up and down.


# Resizing windows and Scrolling:

There are methods to move/resize a window:

win.moveBy(x,y)

Move the window relative to current position x pixels to the right and y pixels down. Negative values are allowed (to move left/up).

win.moveTo(x,y)

Move the window to coordinates (x,y) on the screen.

win.resizeBy(width,height)

Resize the window by given width/height relative to the current size. Negative values are allowed.

win.resizeTo(width,height)

Resize the window to the given size.

There’s also window.onresize event.

Only popups
To prevent abuse, the browser usually blocks these methods. They only work reliably on popups that we opened, that have no additional tabs.

No minification/maximization
JavaScript has no way to minify or maximize a window. These OS-level functions are hidden from Frontend-developers.

Move/resize methods do not work for maximized/minimized windows.






