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

# i-frame:

An <iframe> tag hosts a separate embedded window, with its own separate document and window objects.

We can access them using properties:

iframe.contentWindow to get the window inside the <iframe>.
iframe.contentDocument to get the document inside the <iframe>, iframe.contentWindow.document.
When we access something inside the embedded window, the browser checks if the iframe has the same origin. If that’s not so then the access is denied (writing to location is an exception, it’s still permitted).

iframe.onload vsiframe.contentWindow.onload

The iframe.onload event (on the <iframe>tag) is essentially the same as iframe.contentWindow.onload (on the embedded window object). It triggers when the embedded window fully loads with all resources.

…But we can’t access iframe.contentWindow.onload for an iframe from another origin, so using iframe.onload.

# Windows on subdomains: document.domain:

By definition, two URLs with different domains have different origins.

But if windows share the same second-level domain, for instance, john.site.com, peter.site.com, and site.com (so that their common second-level domain is site.com), we can make the browser ignore that difference, so that they can be treated as coming from the “same origin” for the purposes of cross-window communication.

To make it work, each such window should run the code:

document.domain = 'site.com';

That’s all. Now they can interact without limitations. Again, that’s only possible for pages with the same second-level domain.

So, in summary, if two or more windows share the same second-level domain level, we can tell the browser that they come from the same origin. 

# Wrong document in pitfall (i-frame): 

When an iframe comes from the same origin, and we may access its document, there’s a pitfall. It’s not related to cross-domain things, but important to know.

Upon its creation, an iframe immediately has a document. But that document is different from the one that loads into it!

So if we do something with the document immediately, that will probably be lost.
We shouldn’t work with the document of a not-yet-loaded iframe, because that’s the wrong document. If we set any event handlers on it, they will be ignored.

How to detect the moment when the document is there?

The right document is definitely at the place when iframe.onload triggers. But it only triggers when the whole iframe with all resources is loaded.

# Collection: window.frames: 

An alternative way to get a window object for <iframe>– is to get it from the named collectionwindow.frames:

By number: window.frames[0] – the window object for the first frame in the document.
By name: window.frames.iframeName – the window object for the frame withname="iframeName".
An iframe may have other iframes inside. The corresponding window objects form a hierarchy.

Navigation links are:

window.frames – the collection of “children” windows (for nested frames).
window.parent – the reference to the “parent” (outer) window.
window.top – the reference to the topmost parent window.

# "Sandbox" iframe attribute: 

Allows the prevention of certain actions from happening, due to untrusted code. 
It “sandboxes” the iframe by treating it as coming from another origin and/or applying other limitations.

There’s a “default set” of restrictions applied for <iframe sandbox src="...">. But it can be relaxed if we provide a space-separated list of restrictions that should not be applied as a value of the attribute, like this: <iframe sandbox="allow-forms allow-popups">.

In other words, an empty "sandbox" attribute puts the strictest limitations possible, but we can put a space-delimited list of those that we want to lift.

Here’s a list of limitations:

allow-same-origin:
By default "sandbox" forces the “different origin” policy for the iframe. In other words, it makes the browser to treat the iframe as coming from another origin, even if its src points to the same site. With all implied restrictions for scripts. This option removes that feature.

allow-top-navigation:
Allows the iframe to change parent.location.

allow-forms:
Allows to submit forms from iframe.

allow-scripts:
Allows to run scripts from the iframe.

allow-popups:
Allows to window.open popups from the iframe.

# Day 3: 

# Click-Jacking: 

The “clickjacking” attack allows an evil page to click on a “victim site” on behalf of the visitor.
Clickjacking is for clicks, not for keyboard.
Intersection Observer is looking into solving the click-jacking problem. It can also track interactions off-screen you do not have to always be online, or on the page, to track changes.  Also create a "delay" declaration to check how long it can be visible to you. 


# Weak, old-school defences: 

The oldest defense is a bit of JavaScript which forbids opening the page in a frame (so-called “framebusting”).

window.onbeforeunload
If a visitor initiated navigation away from the page or tries to close the window, the beforeunload handler asks for additional confirmation.

If we cancel the event, the browser may ask the visitor if they are sure.

You can try it by running this code and then reloading the page:

window.onbeforeunload = function() {
  return false;
};
For historical reasons, returning a non-empty string also counts as canceling the event. Some time ago browsers used to show it as a message, but as the modern specification says, they shouldn’t.

Here’s an example:

window.onbeforeunload = function() {
  return "There are unsaved changes. Leave now?";
};
The behavior was changed, because some webmasters abused this event handler by showing misleading and annoying messages. So right now old browsers still may show it as a message, but aside of that – there’s no way to customize the message shown to the user.
When the iframe tries to change top.location, the visitor gets a message asking them whether they want to leave.
In most cases the visitor would answer negatively because they don’t know about the iframe – all they can see is the top page, there’s no reason to leave. So top.location won’t change!
Sandbox attribute:
One of the things restricted by the sandbox attribute is navigation. A sandboxed iframe may not change top.location.
So we can add the iframe with sandbox="allow-scripts allow-forms". That would relax the restrictions, permitting scripts and forms. But we omit allow-top-navigation so that changing top.location is forbidden.
Here’s the code:
<iframe sandbox="allow-scripts allow-forms" src="facebook.html"></iframe>
There are other ways to work around that simple protection too.


# X-Frame options: 

The server-side header X-Frame-Options can permit or forbid displaying the page inside a frame.
It must be sent exactly as HTTP-header: the browser will ignore it if found in HTML <meta> tag. So, <meta http-equiv="X-Frame-Options"...> won’t do anything.

It must be sent exactly as HTTP-header: the browser will ignore it if found in HTML <meta> tag. So, <meta http-equiv="X-Frame-Options"...> won’t do anything.

The header may have 3 values:

DENY:
Never ever show the page inside a frame.

SAMEORIGIN:
Allow inside a frame if the parent document comes from the same origin.

ALLOW-FROM domain:
Allow inside a frame if the parent document is from the given domain.

For instance, Twitter uses X-Frame-Options: SAMEORIGIN.

Here’s the result:
<iframe src="https://twitter.com"></iframe>
Depending on your browser, the iframe above is either empty or alerting you that the browser won’t permit that page to be navigating in this way.
Showing with disabled functionality
The X-Frame-Options header has a side-effect. Other sites won’t be able to show our page in a frame, even if they have good reasons to do so.
So there are other solutions… For instance, we can “cover” the page with a <div> with styles height: 100%; width: 100%;, so that it will intercept all clicks. That <div> is to be removed if window == top or if we figure out that we don’t need the protection.



 









