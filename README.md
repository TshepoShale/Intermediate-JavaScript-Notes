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

# Closing Pop-ups: 
To close a window: win.close().
To check if a window is closed: win.closed.
Technically, the close() method is available for any window, but window.close() is ignored by most browsers if window is not created with window.open(). So it’ll only work on a popup.
The closed property is true if the window is closed. That’s useful to check if the popup (or the main window) is still open or not. A user can close it anytime, and our code should take that possibility into account.
This code loads and then closes the window:

let newWindow = open('/', 'example', 'width=300,height=300');
newWindow.onload = function() {
  newWindow.close();
  alert(newWindow.closed); // true
};

# Accessing windows from popups: 

A popup may access the “opener” window as well using window.opener reference. It is null for all windows except popups.

# Accessing popups from Windows: 

Cross window communication helps when communicating with different browser tabs, and sharing of information. 
The open call returns a reference to the new window. It can be used to manipulate it’s properties, change location and even more.

# Window.open method: 

The syntax to open a popup is: window.open(url, name, params). 


# Popup and Window methods: 

A popup is a separate window with its own independent JavaScript environment. So opening a popup with a third-party non-trusted site is safe.
It’s very easy to open a popup.
A popup can navigate (change URL) and send messages to the opener window.
 setTimeout function to open a new browser window or tab with the URL 'http://google.com' after a delay of 3000 milliseconds, which is equivalent to 3 seconds.

 # Day 2: 

# Cross-Window Communication: 

Cross-Window Messaging: 
The postMessage interface allows windows to talk to each other no matter which origin they are from.

So, it’s a way around the “Same Origin” policy. It allows a window from john-smith.com to talk to gmail.comand exchange information, but only if they both agree and call corresponding JavaScript functions. That makes it safe for users. 
The interface has two parts.

postMessage:
The window that wants to send a message calls postMessage method of the receiving window. In other words, if we want to send the message to win, we should call win.postMessage(data, targetOrigin).
targetOrigin
Specifies the origin for the target window, so that only a window from the given origin will get the message.

The targetOrigin is a safety measure. Remember, if the target window comes from another origin, we can’t read it’s location in the sender window. So we can’t be sure which site is open in the intended window right now: the user could navigate away, and the sender window has no idea about it.

Specifying targetOrigin ensures that the window only receives the data if it’s still at the right site. Important when the data is sensitive.

onmessage:
To receive a message, the target window should have a handler on the message event. It triggers when postMessage is called (and targetOrigin check is successful).

The event object has special properties:

data:
The data from postMessage.

origin:
The origin of the sender, for instance http://javascript.info.

source:
The reference to the sender window. We can immediately source.postMessage(...) back if we want.

To assign that handler, we should use addEventListener, a short syntax window.onmessage does not work.


 









