# Intermediate-JavaScript-Notes
# Week 1: 
/These will be my notes covered for intermediate JavaScript. 
# Day 1:
## The “Same Origin” policy states that:

If we have a reference to another window, e.g. a popup created by window.open or a window inside <iframe>, and that window comes from the same origin, then we have full access to that window. Otherwise, if it comes from another origin, then we can’t access the content of that window: variables, document, anything. The only exception is location: we can change it (thus redirecting the user). But we cannot read the location (so we can’t see where the user is now, no information leak).


## window.onscroll Event: 

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


## Resizing windows and Scrolling:

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

## Closing Pop-ups: 
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

## Accessing windows from popups: 

A popup may access the “opener” window as well using window.opener reference. It is null for all windows except popups.

## Accessing popups from Windows: 

Cross window communication helps when communicating with different browser tabs, and sharing of information. 
The open call returns a reference to the new window. It can be used to manipulate it’s properties, change location and even more.

## Window.open method: 

The syntax to open a popup is: window.open(url, name, params). 


## Popup and Window methods: 

A popup is a separate window with its own independent JavaScript environment. So opening a popup with a third-party non-trusted site is safe.
It’s very easy to open a popup.
A popup can navigate (change URL) and send messages to the opener window.
 setTimeout function to open a new browser window or tab with the URL 'http://google.com' after a delay of 3000 milliseconds, which is equivalent to 3 seconds.

 # Day 2: 

## Cross-Window Communication: 

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

## i-frame:

An <iframe> tag hosts a separate embedded window, with its own separate document and window objects.

We can access them using properties:

iframe.contentWindow to get the window inside the <iframe>.
iframe.contentDocument to get the document inside the <iframe>, iframe.contentWindow.document.
When we access something inside the embedded window, the browser checks if the iframe has the same origin. If that’s not so then the access is denied (writing to location is an exception, it’s still permitted).

iframe.onload vsiframe.contentWindow.onload

The iframe.onload event (on the <iframe>tag) is essentially the same as iframe.contentWindow.onload (on the embedded window object). It triggers when the embedded window fully loads with all resources.

…But we can’t access iframe.contentWindow.onload for an iframe from another origin, so using iframe.onload.

## Windows on subdomains: document.domain:

By definition, two URLs with different domains have different origins.

But if windows share the same second-level domain, for instance, john.site.com, peter.site.com, and site.com (so that their common second-level domain is site.com), we can make the browser ignore that difference, so that they can be treated as coming from the “same origin” for the purposes of cross-window communication.

To make it work, each such window should run the code:

document.domain = 'site.com';

That’s all. Now they can interact without limitations. Again, that’s only possible for pages with the same second-level domain.

So, in summary, if two or more windows share the same second-level domain level, we can tell the browser that they come from the same origin. 

## Wrong document in pitfall (i-frame): 

When an iframe comes from the same origin, and we may access its document, there’s a pitfall. It’s not related to cross-domain things, but important to know.

Upon its creation, an iframe immediately has a document. But that document is different from the one that loads into it!

So if we do something with the document immediately, that will probably be lost.
We shouldn’t work with the document of a not-yet-loaded iframe, because that’s the wrong document. If we set any event handlers on it, they will be ignored.

How to detect the moment when the document is there?

The right document is definitely at the place when iframe.onload triggers. But it only triggers when the whole iframe with all resources is loaded.

## Collection: window.frames: 

An alternative way to get a window object for <iframe>– is to get it from the named collectionwindow.frames:

By number: window.frames[0] – the window object for the first frame in the document.
By name: window.frames.iframeName – the window object for the frame withname="iframeName".
An iframe may have other iframes inside. The corresponding window objects form a hierarchy.

Navigation links are:

window.frames – the collection of “children” windows (for nested frames).
window.parent – the reference to the “parent” (outer) window.
window.top – the reference to the topmost parent window.

## "Sandbox" iframe attribute: 

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

## Click-Jacking: 

The “clickjacking” attack allows an evil page to click on a “victim site” on behalf of the visitor.
Clickjacking is for clicks, not for keyboard.
Intersection Observer is looking into solving the click-jacking problem. It can also track interactions off-screen you do not have to always be online, or on the page, to track changes.  Also create a "delay" declaration to check how long it can be visible to you. 


## Weak, old-school defences: 

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


## X-Frame options: 

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

## Samesite Cookie Attribute: 

The samesite cookie attribute can also prevent clickjacking attacks.

A cookie with such attribute is only sent to a website if it’s opened directly, not via a frame, or otherwise.

If the site, such as Facebook, had samesite attribute on its authentication cookie, like this:

Set-Cookie: authorization=secret; samesite

…Then such cookie wouldn’t be sent when Facebook is open in iframe from another site. So the attack would fail.

The samesite cookie attribute will not have an effect when cookies are not used. This may allow other websites to easily show our public, unauthenticated pages in iframes.

However, this may also allow clickjacking attacks to work in a few limited cases. An anonymous polling website that prevents duplicate voting by checking IP addresses, for example, would still be vulnerable to clickjacking because it does not authenticate users using cookies.

# Day 4: 

## ArrayBuffer: 

Binary data in JavaScript is implemented in a non-standard way, compared to other languages. But when we sort things out, everything becomes fairly simple.
The basic binary object is ArrayBuffer – a reference to a fixed-length contiguous memory area.
We create it like this:

let buffer = new ArrayBuffer(16); // create a buffer of length 16

alert(buffer.byteLength); // 16

This allocates a contiguous memory area of 16 bytes and pre-fills it with zeroes.
ArrayBuffer is not an array of something

Let’s eliminate a possible source of confusion. ArrayBuffer has nothing in common with Array:

It has a fixed length, we can’t increase or decrease it.
It takes exactly that much space in the memory.
To access individual bytes, another “view” object is needed, not buffer[index].
ArrayBuffer is a memory area. What’s stored in it? It has no clue. Just a raw sequence of bytes.
To manipulate an ArrayBuffer, we need to use a “view” object.

A view object does not store anything on its own. It’s the “eyeglasses” that give an interpretation of the bytes stored in the ArrayBuffer.
For instance:

* Uint8Array – treats each byte in ArrayBufferas a separate number, with possible values are from 0 to 255 (a byte is 8-bit, so it can hold only that much). Such value is called a “8-bit unsigned integer”.
* Uint16Array – treats every 2 bytes as an integer, with possible values from 0 to 65535. That’s called a “16-bit unsigned integer”.
* Uint32Array – treats every 4 bytes as an integer, with possible values from 0 to 4294967295. That’s called a “32-bit unsigned integer”.
* Float64Array – treats every 8 bytes as a floating point number with possible values from 5.0x10-324to 1.8x10308.
So, the binary data in an ArrayBuffer of 16 bytes can be interpreted as 16 “tiny numbers”, or 8 bigger numbers (2 bytes each), or 4 even bigger (4 bytes each), or 2 floating-point values with high precision (8 bytes each).

But if we’re going to write into it, or iterate over it, basically for almost any operation – we must use a view, e.g:

let buffer = new ArrayBuffer(16); // create a buffer of length 16

let view = new Uint32Array(buffer); // treat buffer as a sequence of 32-bit integers

alert(Uint32Array.BYTES_PER_ELEMENT); // 4 bytes per integer

alert(view.length); // 4, it stores that many integers

alert(view.byteLength); // 16, the size in bytes

// let's write a value

view[0] = 123456;

// iterate over values

for(let num of view) {

  alert(num); // 123456, then 0, 0, 0 (4 values total)

}

The common term for all these views (Uint8Array, Uint32Array, etc) is TypedArray. They share the same set of methods and properties.
A typed array constructor (be it Int8Array or Float64Array, doesn’t matter) behaves differently depending on argument types.
1. If an ArrayBuffer argument is supplied, the view is created over it. We used that syntax already.

Optionally we can provide byteOffset to start from (0 by default) and the length (till the end of the buffer by default), then the view will cover only a part of the buffer.
2. If an Array, or any array-like object is given, it creates a typed array of the same length and copies the content.

We can use it to pre-fill the array with the data:

let arr = new Uint8Array([0, 1, 2, 3]);
alert( arr.length ); // 4, created binary array of the same length
alert( arr[1] ); // 1, filled with 4 bytes (unsigned 8-bit integers) with given values
3. If another TypedArray is supplied, it does the same: creates a typed array of the same length and copies values. Values are converted to the new type in the process, if needed.

let arr16 = new Uint16Array([1, 1000]);
let arr8 = new Uint8Array(arr16);
alert( arr8[0] ); // 1
alert( arr8[1] ); // 232, tried to copy 1000, but can't fit 1000 into 8 bits (explanations below)
4. For a numeric argument length – creates the typed array to contain that many elements. Its byte length will be length multiplied by the number of bytes in a single item TypedArray.BYTES_PER_ELEMENT:

let arr = new Uint16Array(4); // create typed array for 4 integers
alert( Uint16Array.BYTES_PER_ELEMENT ); // 2 bytes per integer
alert( arr.byteLength ); // 8 (size in bytes)
5. Without arguments, creates an zero-length typed array.

We can create a TypedArray directly, without mentioning ArrayBuffer. But a view cannot exist without an underlying ArrayBuffer, so gets created automatically in all these cases except the first one (when provided).

To access the ArrayBuffer, there are properties:

arr.buffer – references the ArrayBuffer.
arr.byteLength – the length of the ArrayBuffer.
So, we can always move from one view to another:

let arr8 = new Uint8Array([0, 1, 2, 3]);
// another view on the same data
let arr16 = new Uint16Array(arr8.buffer);

Here’s the list of typed arrays:

* Uint8Array, Uint16Array, Uint32Array – for integer numbers of 8, 16 and 32 bits.
* Uint8ClampedArray – for 8-bit integers, “clamps” them on assignment (see below).
* Int8Array, Int16Array, Int32Array – for signed integer numbers (can be negative).
* Float32Array, Float64Array – for signed floating-point numbers of 32 and 64 bits.
No int8 or similar single-valued types

Please note, despite of the names like Int8Array, there’s no single-value type like int, or int8 in JavaScript.

That’s logical, as Int8Array is not an array of these individual values, but rather a view on ArrayBuffer.

## Out-of-bounds behaviour: 

What if we attempt to write an out-of-bounds value into a typed array? There will be no error. But extra bits are cut-off.

For instance, let’s try to put 256 into Uint8Array. In binary form, 256 is 100000000 (9 bits), but Uint8Array only provides 8 bits per value, which makes the available range from 0 to 255.  
For bigger numbers, only the rightmost (less significant) 8 bits are stored, and the rest is cut off.

## Typed Array methods: 

TypedArray has regular Array methods, with notable exceptions.

We can iterate, map, slice, find, reduce, etc.

We cant splice (delete) a value because typed arrays are views on a buffer, and these are fixed. contiguous areas of memory. We can only assign a zero on the splice section of a method. There is also no concat method on TypedArrays. 

There are a few things we can’t do though:

No splice – we can’t “delete” a value, because typed arrays are views on a buffer, and these are fixed, contiguous areas of memory. All we can do is to assign a zero.
No concat method.
There are two additional methods:

arr.set(fromArr, [offset]) copies all elements from fromArr to the arr, starting at position offset (0 by default).
arr.subarray([begin, end]) creates a new view of the same type from begin to end (exclusive). That’s similar to slice method (that’s also supported), but doesn’t copy anything – just creates a new view, to operate on the given piece of data.
These methods allow us to copy typed arrays, mix them, create new arrays from existing ones, and so on.

## DataView: 

DataView is a special super-flexible “untyped” view over ArrayBuffer. It allows accessing the data on any offset in any format.

For typed arrays, the constructor dictates what the format is. The whole array is supposed to be uniform. The i-th number is arr[i].
With DataView we access the data with methods like .getUint8(i) or .getUint16(i). We choose the format at method call time instead of the construction time.
The syntax:

new DataView(buffer, [byteOffset], [byteLength])

buffer – the underlying ArrayBuffer. Unlike typed arrays, DataView doesn’t create a buffer on its own. We need to have it ready.
byteOffset – the starting byte position of the view (by default 0).
byteLength – the byte length of the view (by default till the end of buffer).
DataView is great when we store mixed-format data in the same buffer. E.g we store a sequence of pairs (16-bit integer, 32-bit float). Then DataView allows to access them easily. 




                                                                                --- END OF WEEK 1 ---

# Week 2: 

## Day 1:

## File and FileReader: 

A file object inherits from Blob and is extended with filesystem related capabilities. There are two ways to obtain it. 
First there is a constructor, similar to Blob:
new File(fileParts, fileName, [options])
* fileParts - is an array of Blob/BufferSource/String values.
* fileName - file name string.
* options - optional object:
* lastModified - the timestamp (integer date) of last modification.
Second, more often we get a file from <input type="file"> or drag’n’drop or other browser interfaces. In that case, the file gets this information from OS
As File inherits from Blob, File objects have the same properties, plus:
* name – the file name,
* lastModified – the timestamp of last modification.

## FileReader: 

FileReader is an object with the sole purpose of reading data from Blob (and hence File too) objects. It delivers the data using events as reading from disk may take time.
The constructor is as follows:
let reader = new FileReader(); // no arguments
The main method is as follows:

* readAsArrayBuffer(blob) – read the data in binary format ArrayBuffer; for binary files, to do low-level binary operations. For high-level operations, like slicing, File inherits from Blob, so we can call them directly, without reading.
* readAsText(blob, [encoding]) – read the data as a text string with the given encoding (utf-8by default).
* readAsDataURL(blob) – read the binary data and encode it as base64 data url.
* abort() – cancel the operation.
* readAsText - for text files, when we’d like to get a string.
* readAsDataURL - when we’d like to use this data in src for img or another tag. There’s an alternative to reading a file for that, as discussed in chapter Blob: URL.createObjectURL(file).


  As the reading proceeds, there are events:

* loadstart – loading started.
* progress – occurs during reading.
* load – no errors, reading complete.
* abort – abort() called.
* error – error has occurred.
* loadend – reading finished with either success or failure.

Most common events are load and error. 

When the reading is finished, we can access the result as:

* reader.result is the result (if successful)
* reader.error is the error (if failed).

NB: FileReader can be used to convert files to another format. 

FileReaderSync is an interface in JavaScript that provides synchronous methods to read the contents of a File or a Blob object. It is a part of the File API, which allows web applications to interact with files on the user's local system.

In JavaScript, file reading operations are typically asynchronous, meaning that the browser initiates the operation and continues executing the remaining code without waiting for the file reading to complete. This is useful for non-blocking operations, ensuring that the web page remains responsive.

However, in some cases, synchronous file reading may be necessary, especially in environments where asynchronous operations are not suitable. For instance, in web workers or other contexts where synchronous I/O operations are allowed, FileReaderSync can be used to read files synchronously.

// Assuming you have a File object named 'file'
const fileReader = new FileReaderSync();
const content = fileReader.readAsText(file);
console.log(content);

In the above code, readAsText is a synchronous method provided by FileReaderSync that reads the contents of the specified file as a text string. The content of the file is stored in the content variable, which can be further processed or displayed as needed.

Please note that synchronous file reading can block the main thread, leading to potential performance issues and unresponsiveness in your web application. It's generally recommended to use asynchronous file reading methods whenever possible to ensure a smooth user experience.
FileReaderSync is available inside Web Workers
For Web Workers, there also exists a synchronous variant of FileReader, called FileReaderSync.
Its reading methods read* do not generate events, but rather return a result, as regular functions do.
That’s only inside a Web Worker though, because delays in synchronous calls, that are possible while reading from files, in Web Workers are less important. They do not affect the page.
In many cases though, we don’t have to read the file contents. Just as we did with blobs, we can create a short url with URL.createObjectURL(file) and assign it to <a> or <img>. This way the file can be downloaded or shown up as an image, as a part of canvas etc.
And if we’re going to send a File over a network, that’s also easy: network API like XMLHttpRequest or fetch natively accepts File objects.

## Fetch: 
This makes network requests using API, such as fetching resources from a server. 
There’s an umbrella term “AJAX” (abbreviated Asynchronous Javascript And Xml) for that. We don’t have to use XML though: the term comes from old times, that’s that word is there.

There are multiple ways to send a network request and get information from the server.

The fetch() method is modern and versatile, so we’ll start with it. It evolved for several years and continues to improve, right now the support is pretty solid among browsers.

The basic syntax is:

let promise = fetch(url, [options])

* url – the URL to access.
* options – optional parameters: method, headers etc.
The browser starts the request right away and returns a promise.
Getting a response is usually a two-stage process.
First, the promise resolves with an object of the built-in Response class as soon as the server responds with headers.
So we can check HTTP status, to see whether it is successful or not, check headers, but don’t have the body yet.
The promise rejects if the fetch was unable to make HTTP-request, e.g. network problems, or there’s no such site. HTTP-errors, even such as 404 or 500, are considered a normal flow.
We can see them in response properties:
· ok – boolean, true if the HTTP status code is 200-299.
status – HTTP status code.

Response provides multiple promise-based methods to access the body in various formats:

* response.json() – parse the response as JSON object,
* response.text() – return the response as text,
* response.formData() – return the response as FormData object (form/multipart encoding, explained in the next chapter),
* response.blob() – return the response as Blob(binary data with type),
* response.arrayBuffer() – return the response as ArrayBuffer (pure binary data),
additionally, response.body is a ReadableStreamobject, it allows to read the body chunk-by-chunk.
Important:
We can choose only one body-parsing method.
If we got the response with response.text(), then response.json() won’t work, as the body content has already been processed.

## Headers:
There’s a Map-like headers object in response.headers. We can get individual headers or iterate over them.
…But there’s a list of forbidden HTTP headers that we can’t set:

· Accept-Charset, Accept-Encoding
· Access-Control-Request-Headers
· Access-Control-Request-Method
· Connection
· Content-Length
· Cookie, Cookie2
· Date
· DNT
· Expect
· Host
· Keep-Alive
· Origin
· Referer
· TE
· Trailer
· Transfer-Encoding
· Upgrade
· Via
· Proxy-*
· Sec-*
These headers ensure proper and safe HTTP, so they are controlled exclusively by the browser.

## Post-Requests: 
To make a POST request, or a request with another method, we need to use fetch options:

* method – HTTP-method, e.g. POST,
* body – one of:
* a string (e.g. JSON),
* FormData object, to submit the data as form/multipart,
* Blob/BufferSource to send binary data,
URLSearchParams, to submit the data in x-www-form-urlencoded encoding, rarely used.
NB: Please note, if the body is a string, then Content-Typeis set to text/plain;charset=UTF-8 by default. So we use headers option to send application/jsoninstead, that’s the correct content type for JSON-encoded data.

Response properties:

* response.status – HTTP code of the response,
* response.ok – true is the status is 200-299.
* response.headers – Map-like object with HTTP headers.
* response.formData() – return the response as FormData object (form/multipart encoding,
* response.headers – Map-like object with HTTP headers.

Methods to get response body:
* response.json() – parse the response as JSON object,
* response.text() – return the response as text,
* response.blob() – return the response as Blob(binary data with type),
* response.arrayBuffer() – return the response as ArrayBuffer (pure binary data),

Fetch options so far:

* method – HTTP-method,
* headers – an object with request headers (not any header is allowed),
* body – string, FormData, BufferSource, Blob or UrlSearchParams object to send.

 Here's an example of an async function named getUsers that takes an array of GitHub logins, fetches the users from GitHub using the Fetch API, and returns an array of GitHub users:
 async function getUsers(names) {
  let users = [];

  // Create an array of promises for fetching user data from GitHub API
  let requests = names.map(name => {
    return fetch(`https://api.github.com/users/${name}`)
      .then(response => {
        if (response.ok) {
          return response.json();
        } else {
          throw new Error(`Failed to fetch user: ${name}`);
        }
      })
      .then(user => {
        users.push(user);
      })
      .catch(error => {
        console.error(error);
      });
  });

  // Wait for all requests to complete
  await Promise.all(requests);

  return users;
}

// Example usage of getUsers function
async function main() {
  const names = ['user1', 'user2', 'user3']; // GitHub logins
  try {
    const githubUsers = await getUsers(names);
    console.log('GitHub Users:', githubUsers);
  } catch (error) {
    console.error('Error:', error);
  }
}

// Call the main function
main();


In this example, the getUsers function takes an array of GitHub logins as input. It uses the map function to create an array of promises, each of which represents a fetch request to the GitHub API for a specific user. The Promise.all function is then used to wait for all fetch requests to complete. The resulting array of GitHub users is returned.

Please note that you should replace 'user1', 'user2', and 'user3' in the names array with the actual GitHub logins you want to fetch. Also, make sure to handle rate limits and errors appropriately in a production environment.

# Day 2: 

## FormData: 

What is FormData? : Sending HTML forms: with or without files, with additional fields and so on. FormDataobjects can help with that.
The constructor is:

let formData = new FormData([form]);

If HTML form element is provided, it automatically captures its fields. As you may have already guessed, FormData is an object to store and send form data.

The special thing about FormData is that network methods, such as fetch, can accept a FormDataobject as a body. It’s encoded and sent out with Content-Type: form/multipart. So, from the server point of view, that looks like a usual form submission.

## FormData Methods: 
* formData.append(name, value) – add a form field with the given name and value.
* formData.append(name, blob, fileName) – add a field as if it were <input type="file">, the third argument fileName sets file name (not form field name), as it it were a name of the file in user’s filesystem.
* formData.delete(name) – remove the field with the given name.
* formData.get(name) – get the value of the field with the given name.
* formData.has(name) – if there exists a field with the given name, returns true, otherwise false.
  AJAX: Focuses on client server communication.
  JSON: Focuses on data, and its processing attributes.
  A form is technically allowed to have many fields with the same name, so multiple calls to append add more same-named fields.

There’s also method set, with the same syntax as append. The difference is that .set removes all fields with the given name, and then appends a new field. So it makes sure there’s only field with such name:

* formData.set(name, value),
* formData.set(name, blob, fileName).

## Sending a Form, with a file: 
The form is always sent as Content-Type: form/multipart, this encoding allows to send files. So, <input type="file"> fields are sent also, similar to a usual form submission.

## Sending a Form, with Blob data: 

Please note how the image Blob is added:

formData.append("image", imageBlob, "image.png");

That’s same as if there were <input type="file" name="image"> in the form, and the visitor submitted a file named image.png (3rd argument) from their filesystem.
Summary
FormData objects are used to capture HTML form and submit it using fetch or another network method.

We can either create new FormData(form) from an HTML form, or create an empty object, and then append fields with methods:


* formData.append(name, value)
* formData.append(name, blob, fileName)
* formData.set(name, value)
* formData.set(name, blob, fileName)
Two peculiarities here:

The set method removes fields with the same name, append doesn’t.
To send a file, 3-argument syntax is needed, the last argument is a file name, that normally is taken from user filesystem for <input type="file">.
Other methods are:

* formData.delete(name)
* formData.get(name)
* formData.has(name)

## Fetch: Download Progress: 
To track download progress, we can use response.body property. It’s a “readable stream” – a special object that provides body chunk-by-chunk, as it comes.
Unlike response.text(), response.json() and other methods, response.body gives full control over the reading process, and we can count how much is consumed at any moment.


The result of await reader.read() call is an object with two properties:

* done – true when the reading is complete.
* value – a typed array of bytes: Uint8Array.
We wait for more chunks in the loop, until done is true.

To log the progress, we just need for every value add its length to the counter.

## Fetch: Abort: 

Remember, fetch returns a promise. And JavaScript generally has no concept of “aborting” a promise. So how can we cancel a fetch?

There’s a special built-in object for such purposes: AbortController.

Step 1: create a controller:
let controller = new AbortController();

A controller is an extremely simple object. It has a single method abort(), and a single property signal. When abort() is called, the abortevent triggers on controller.signal:
let controller = newAbort Controller(); 
setTimeout(() => controller.abort(), 1000);

try {
let response = await fetch('/article/fetch-abort/demo/hang',
{ signal: controller.signal } ); 
} 

catch(err) 
{
  if (err.name == 'AbortError') { //handle abort()
alert("Aborted!");
}

else 
{ throw err; }
  }
}
AbortController is scalable, it allows to cancel multiple fetches at once.


# Day 3: 

## Fetch: Cross-Origin requests:

The core concept here is origin – a domain/port/protocol triplet.

Cross-origin requests – those sent to another domain (even a subdomain) or protocol or port – require special headers from the remote side. That policy is called “CORS”: Cross-Origin Resource Sharing.
This concept protects our websites from evil hackers. 
### Using Forms: 
One way to communicate with another server was to submit a <form> there. People submitted it into <iframe>, just to stay on the current page, like this:
<!-- form target -->
<iframe name="iframe"></iframe>

<!-- a form could be dynamically generated and submitted by JavaScript --> 
<form target="iframe" method="POST" action="http://facebook.com/...">
...
</form>
So, it was possible to make a GET/POST request to another site, even without networking methods. But as it’s forbidden to access the content of an <iframe>from another site, it wasn’t possible to read the response.

As we can see, forms allowed to send data anywhere, but not receive the response. To be precise, there were actually tricks for that (required special scripts at both the iframe and the page), but let these dinosaurs rest in peace.

## Simple Requests: 
NB: Cross-Origin Resource Sharing is a security feature implemented by web browsers to control how web pages in one domain can request and interact with resources hosted on another domain.

When a web page makes a cross-origin HTTP request (a request to a different domain, protocol, or port), the browser includes specific HTTP headers in the request to negotiate permissions with the server.
There are two types of cross-domain requests:

* Simple requests.

* All the others.

Simple Requests are, well, simpler to make, so let’s start with them.

A simple request is a request that satisfies two conditions:

* Simple method: GET, POST or HEAD
* Simple headers – the only allowed custom headers are:
  Accept,
* Accept-Language,
* Content-Language,
* Content-Type with the value application/x-www-form-urlencoded, multipart/form-data or text/plain.
Any other request is considered “non-simple”. For instance, a request with PUT method or with an API-Key HTTP-header does not fit the limitations.
The essential difference is that a “simple request” can be made with a <form> or a <script>, without any special methods.
When we try to make a non-simple request, the browser sends a special “preflight” request that asks the server – does it agree to accept such cross-origin requests, or not?
And, unless the server explicitly confirms that with headers, a non-simple request is not sent.

### CORS for Simple Requests: 

If a request is cross-origin, the browser always adds Origin header to it.

For instance, if we request https://anywhere.com/request from https://javascript.info/page, the headers will be like:

* GET /request
* Host: anywhere.com
* Origin: https://javascript.info

As you can see, Origin contains exactly the origin (domain/protocol/port), without a path.

The server can inspect the Origin and, if it agrees to accept such a request, adds a special header Access-Control-Allow-Origin to the response. That header should contain the allowed origin (in our case https://javascript.info), or a star *. Then the response is successful, otherwise an error.

The browser plays the role of a trusted mediator here:
1. It ensures that the correct Origin is sent with a cross-domain request.
2. It checks for correct Access-Control-Allow-Origin in the response, if it is so, then JavaScript access, otherwise forbids with an error.

Here’s an example of a permissive server response:

200 OK
Content-Type:text/html; charset=UTF-8
Access-Control-Allow-Origin: https://javascript.info

JavaScript uses the fetch() method, to consult the browser. What the browser then does is send and HHTP request to the server. If it validates then using the Origin, the source, and the path. If it validated, the the Post method is implemented, and the the GET request is sent to the server. The request is then sent back to the browser using the access-control-allow-origin, or the website url, returning a header specified. If not the it will fail. The success response will be in HTTP code format, HTTP 200 status OK. 

### Response Headers: 

JavaScript can only access simple-response headers. 
In the context of Cross-Origin Resource Sharing (CORS) in JavaScript, simple response headers refer to the subset of response headers that the browser considers safe to expose to the requesting client without the need for CORS protocol. These headers don't trigger a CORS preflight request (a preliminary request that checks if the actual request is safe to send) and are automatically included in the response by the browser if the request meets certain criteria.

Cache-Control: Used to specify caching directives in the response. For example, "public" indicates that the response may be cached by any cache, even if it's normally non-cacheable.

1. Content-Language: Specifies the language of the content.

2. Content-Type: Indicates the media type of the resource.

3. Expires: Gives the date/time after which the response is considered stale.

4. Last-Modified: Indicates the last modified date of the resource.

5. Pragma: Provides implementation-specific directives that might apply to any recipient along the request/response chain.

6. Access-Control-Allow-Origin: Though this header is part of CORS and is used for handling cross-origin requests, it is considered a simple response header because it doesn't require a preflight request in certain circumstances. When the server specifies a specific origin (rather than "*") in the Access-Control-Allow-Origin header and the request is a simple GET, HEAD, or POST request, this header is considered simple.

Any other response header is forbidden.

Please note: no Content-Length

Please note: there’s no Content-Length header in the list!

This header contains the full response length. So, if we’re downloading something and would like to track the percentage of progress, then additional permission is required to access that header (see below).

NB: To grant JavaScript access to any other response header, the server must list it in the Access-Control-Expose-Headers header.

For example:

200 OK
Content-Type:text/html; charset=UTF-8
Content-Length: 12345
API-Key: 2c9de507f2c54aa1
Access-Control-Allow-Origin: https://javascript.info
Access-Control-Expose-Headers: Content-Length,API-Key

With such Access-Control-Expose-Headers header, the script is allowed to access Content-Length and API-Key headers of the response.


### Cross-Domain PATCH Request: 

let response = await fetch ('https://site.com/service.json',{
method : PATCH,   
headers: {
'Content-Type' : application.json
'API-key' : 'secret' 
}
});
PATCH Method, sends out to the main request. 


Here's how a typical CORS request works:

* Preflight Request: Before the actual request (like GET or POST) is sent, the browser sends an HTTP OPTIONS request to the server. This preflight request includes headers indicating what kind of request your JavaScript code wants to make.

* Server Checks: The server then checks these headers to see if it allows requests from the origin (your website). If the server is configured to permit requests from your domain, it will respond to the preflight request with the appropriate CORS headers, indicating that the actual request can proceed.

* Actual Request: If the server responds with the correct CORS headers permitting the request, your browser sends the actual GET, POST, or other request specified by your JavaScript code.

This process is in place to enhance security on the web, ensuring that only trusted sources can interact with a server's resources.
In JavaScript, you often don't have to worry about these details because modern frameworks and libraries handle CORS for you. However, it's good to understand the concept, especially if you're dealing with raw HTTP requests in your applications.

## Credentials: 

In the context of web development, when you make requests from a client-side JavaScript application to a different domain, you often encounter issues related to security. One such issue is related to credentials.

When you make a cross-origin request (a request from one domain to another), browsers, by default, do not include credentials such as cookies or HTTP authentication information. This is a security measure to prevent unauthorized access to user data.

However, there are scenarios where you might want to include credentials in your cross-origin requests. For example, if your frontend code is on one domain and your backend API is on another domain, and you want to authenticate users using sessions or cookies, you need to send credentials with your requests.

To include credentials in a cross-origin request, you need to set the `credentials` option in your JavaScript code. The `credentials` option can be set to one of the following values:

- `'same-origin'`: This option includes credentials in the request only if the request origin is the same as the target origin. If they're different, credentials won't be sent.

- `'include'`: This option always includes credentials with the request, regardless of the origin. Use this when you want to send cookies or HTTP authentication information.

- `'omit'`: This option never includes credentials in the request, regardless of the origin. Use this when you don't want to send any credentials.

Here's an example of how you might use the `credentials` option in a fetch request in JavaScript:

```javascript
fetch('https://api.example.com/data', {
  method: 'GET',
  credentials: 'include', // or 'same-origin' or 'omit' based on your requirements
})
  .then(response => {
    // Handle the response
  })
  .catch(error => {
    // Handle errors
  });
```

In this example, the `credentials: 'include'` option is set, indicating that the request should include credentials. This is essential if you're dealing with authentication mechanisms that rely on cookies or other credentials stored in the user's browser.

Always be careful when handling credentials in your JavaScript code to ensure the security of your users' data. Make sure to use secure connections (HTTPS) and implement proper server-side security measures to protect sensitive information.

 Why do we need Origin?
As you probably know, there’s HTTP-header Referer, that usually contains a URL of the page which initiated a network request.

For instance, when fetching http://google.comfrom http://javascript.info/some/url, the headers look like this:

Accept: */*
Accept-Charset: utf-8
Accept-Encoding: gzip,deflate,sdch
Connection: keep-alive
Host: google.com
Origin: http://javascript.info
Referer: http://javascript.info/some/url

As you can see, both Referer and Origin are present.

We need Origin, because sometimes Referer is absent. For instance, when we fetch HTTP-page from HTTPS (access less secure from more secure), then there’s no Referer.
The Content Security Policy may forbid sending a Referer.
As we’ll see, fetch has options that prevent sending the Referer and even allow to change it (within the same site).
By specification, Referer is an optional HTTP-header.
Exactly because Referer is unreliable, Origin was invented. The browser guarantees correct Origin for cross-origin requests.

# Day 4: 

## Patterns, Flags and RegEx: 

In JavaScript, patterns and flags are used with regular expressions (regex) to search for and manipulate text. 

* RegEx: A regular expression (also “regexp”, or just “reg”) consists of a pattern and optional flags. Slashes "/" tell JavaScript that we are creating a regular expression. They play the same role as quotes for strings.
* Flags: Additional settings that modify the behavior of the pattern matching. They are used as optional parameters to the regex constructor or as a suffix to the pattern.
* Patterns in regular expressions (regex) are sequences of characters that define a search pattern. They are used to match and manipulate strings of text.

 There are only 6 flagsin JavaScript:
* i
With this flag the search is case-insensitive: no difference between A and a (see the example below).

* g
With this flag the search looks for all matches, without it – only the first one 

* m
Multiline mode (covered in the chapter Multiline mode, flag "m").

* s
“Dotall” mode, allows . to match newlines (covered in the chapter Character classes).

* u
Enables full unicode support. The flag enables correct processing of surrogate pairs. More about that in the chapter  Unicode: flag "u".

* y
Sticky mode (covered in the chapter Sticky flag "y", searching at position)


## Methods of RegEx and flags: 

First, regular expressions are objects of the built-in RegExp class, it provides many methods. Besides that, there are methods in regular strings can work with regexps.

o search for all matches:

Use regexp g flag and:

Get a flat array of matches – str.match(reg)
Get an array or matches with details – str.matchAll(reg).
To search for the first match only:

Get the full first match – str.match(reg) (without g flag).
Get the string position of the first match – str.search(reg).
Check if there’s a match – regexp.test(str).
Find the match from the given position – regexp.exec(str) (set regexp.lastIndex to position).
* To replace all matches:
Replace with another string or a function result – str.replace(reg, str|func)
* To split the string by a separator:
str.split(str|reg)
str.search(reg)

NB: The important limitation: search only finds the first match.

We can’t find next matches using search, there’s just no syntax for that. But there are other methods that can.

str.match(reg), no “g” flag

The behavior of str.match varies depending on whether reg has g flag or not.
First, if there’s no g flag, then str.match(reg) looks for the first match only.

The result is an array with that match and additional properties:
* index – the position of the match inside the string,
* input – the subject string.
If a part of the pattern is delimited by parentheses (...), then it becomes a separate element in the array.
If parentheses have a name, designated by (?<name>...) at their start, then result.groups[name] has the content.

Due to the i flag the search is case-insensitive, so it finds JavaScript. The part of the match that corresponds to SCRIPTbecomes a separate array item.

So, this method is used to find one full match with all details.

str.match(reg) with “g” flag

When there’s a "g" flag, then str.match returns an array of all matches. There are no additional properties in that array, and parentheses do not create any elements.

So, with g flag str.match returns a simple array of all matches, without details.

If we want to get information about match positions and contents of parentheses then we should use matchAll method that we’ll cover below.

If there are no matches, str.match returns null

Please note, that’s important. If there are no matches, the result is not an empty array, but null. Keep that in mind to evade pitfalls like this:

let str = "Hey-hey-hey!";

alert( str.match(/Z/g).length ); // Error: Cannot read property 'length' of null
Here str.match(/Z/g) is null, it has no length property.

str.matchAll(regexp)

The method str.matchAll(regexp) is used to find all matches with all details.

matchAll returns an iterable, not array

matchAll is supernew, may need a polyfill

The method may not work in old browsers. A polyfill might be needed (this site uses core-js).
Or you could make a loop with regexp.exec, explained below.

str.split(regexp|substr, limit)

Splits the string using the regexp (or a substring) as a delimiter.

We already used split with strings, like this:

alert('12-34-56'.split('-')) // array of [12, 34, 56]

But we can split by a regular expression, the same way:

alert('12-34-56'.split(/-/)) // array of [12, 34, 56]

str.replace(str|reg, str|func)

This is a generic method for searching and replacing, one of most useful ones. The swiss army knife for searching and replacing.

We can use it without regexps, to search and replace a substring:

// replace a dash by a colon

alert('12-34-56'.replace("-", ":")) // 12:34-56

There’s a pitfall though.

When the first argument of replace is a string, it only looks for the first match.

You can see that in the example above: only the first "-" is replaced by ":".

To find all dashes, we need to use not the string "-", but a regexp /-/g, with an obligatory g flag:

 // replace all dashes by a colon

alert( '12-34-56'.replace( /-/g, ":" ) )  // 12:34:56

The second argument is a replacement string. We can use special characters in it:

Symbol	Inserts
$$	"$"
$&	the whole match
$`	a part of the string before the match
$'	a part of the string after the match
$n	if n is a 1-2 digit number, then it means the contents of n-th parentheses counting from left to right, otherwise it means a parentheses with the given name
For instance if we use $& in the replacement string, that means “put the whole match here”.

Let’s use it to prepend all entries of "John" with "Mr.":

let str = "John Doe, John Smith and John Bull";

// for each John - replace it with Mr. and then John

alert(str.replace(/John/g, 'Mr.$&'));  // Mr.John Doe, Mr.John Smith and Mr.John Bull

Quite often we’d like to reuse parts of the source string, recombine them in the replacement or wrap into something.

To do so, we should:

First, mark the parts by parentheses in regexp.
Use $1, $2 (and so on) in the replacement string to get the content matched by 1st, 2nd and so on parentheses.
For instance:

let str = "John Smith";

// swap first and last name

alert(str.replace(/(john) (smith)/i, '$2, $1')) // Smith, John

For situations that require “smart” replacements, the second argument can be a function.

It will be called for each match, and its result will be inserted as a replacement.

The function is called with arguments func(str, p1, p2, ..., pn, offset, input, groups):

str – the match,
p1, p2, ..., pn – contents of parentheses (if there are any),
offset – position of the match,
input – the source string,
groups – an object with named groups (see chapter Capturing groups).
If there are no parentheses in the regexp, then there are only 3 arguments: func(str, offset, input).

Using a function gives us the ultimate replacement power, because it gets all the information about the match, has access to outer variables and can do everything.

regexp.exec(str)

We’ve already seen these searching methods:

* search – looks for the position of the match,
* match – if there’s no g flag, returns the first match with parentheses and all details,
* match – if there’s a g flag – returns all matches, without details parentheses,
* matchAll – returns all matches with details.

The regexp.exec method is the most flexible searching method of all. Unlike previous methods, exec should be called on a regexp, rather than on a string.

It behaves differently depending on whether the regexp has the g flag.

If there’s no g, then regexp.exec(str) returns the first match, exactly as str.match(reg). Such behavior does not give us anything new.

But if there’s g, then:

regexp.exec(str) returns the first match and remembers the position after it in regexp.lastIndex property.
* The next call starts to search from regexp.lastIndex and returns the next match.
* If there are no more matches then regexp.exec returns null and regexp.lastIndex is set to 0.

* regexp.test(str)
The method regexp.test(str) looks for a match and returns true/false whether it finds it.

Summary

There’s a variety of many methods on both regexps and strings.

Their abilities and methods overlap quite a bit, we can do the same by different calls. Sometimes that may cause confusion when starting to learn the language.

Then please refer to the recipes at the beginning of this chapter, as they provide solutions for the majority of regexp-related tasks.

Daily Notes - Methods of RegExp and String
Daily Notes - Methods of RegExp and String

The second argument is a replacement string. We can use special characters in it:

Symbol	Inserts
$$	"$"
$&	the whole match
$`	a part of the string before the match
$'	a part of the string after the match
$n	if n is a 1-2 digit number, then it means the contents of n-th parentheses counting from left to right, otherwise it means a parentheses with the given name
For instance if we use $& in the replacement string, that means “put the whole match here”.

Let’s use it to prepend all entries of "John" with "Mr.":

let str = "John Doe, John Smith and John Bull";
// for each John - replace it with Mr. and then John
alert(str.replace(/John/g, 'Mr.$&'));  // Mr.John Doe, Mr.John Smith and Mr.John Bull


Quite often we’d like to reuse parts of the source string, recombine them in the replacement or wrap into something.

To do so, we should:
First, mark the parts by parentheses in regexp.
Use $1, $2 (and so on) in the replacement string to get the content matched by 1st, 2nd and so on parentheses.


For instance:
let str = "John Smith";
// swap first and last name
alert(str.replace(/(john) (smith)/i, '$2, $1')) // Smith, John

For situations that require “smart” replacements, the second argument can be a function.
It will be called for each match, and its result will be inserted as a replacement.


## Character Classes: 
Consider a practical task – we have a phone number "+7(903)-123-45-67", and we need to turn it into pure numbers: 79035419441.
To do so, we can find and remove anything that’s not a number. Character classes can help with that.
A character class is a special notation that matches any symbol from a certain set.


## Word boundary \b:

* A word boundary \b – is a special character class.
* It does not denote a character, but rather a boundary between characters.
For instance, \bJava\b matches Java in the string Hello, Java!, but not in the script Hello, JavaScript!.
alert( "Hello, Java!".match(/\bJava\b/) ); // Java
alert( "Hello, JavaScript!".match(/\bJava\b/) ); // null
The boundary has “zero width” in a sense that usually a character class means a character in the result (like a wordly character or a digit), but not in this case.
The boundary is a test.
When regular expression engine is doing the search, it’s moving along the string in an attempt to find the match. At each string position it tries to find the pattern.
When the pattern contains \b, it tests that the position in string is a word boundary, that is one of three variants:
Immediately before is \w, and immediately after – not \w, or vise versa.
At string start, and the first string character is \w.
At string end, and the last string character is \w.

## Inverse Classes:

For every character class there exists an “inverse class”, denoted with the same letter, but uppercased.
The “reverse” means that it matches all other characters, for instance:

* \D:
Non-digit: any character except \d, for instance a letter.

* \S:
Non-space: any character except \s, for instance a letter.

* \W:
Non-wordly character: anything but \w.

* \B:
Non-boundary: a test reverse to \b.

In the beginning of the chapter we saw how to get all digits from the phone +7(903)-123-45-67.

One way was to match all digits and join them:

let str = "+7(903)-123-45-67";
alert( str.match(/\d/g).join('') ); // 79031234567

An alternative, shorter way is to find non-digits \D and remove them from the string:

let str = "+7(903)-123-45-67";
alert( str.replace(/\D/g, "") ); // 79031234567

NB: A space is a character. Equal in importance with any other character.
NB: The dot "." is a special character class that matches “any character except a newline”.

## The dotfall "s" flag:
That’s what s flag does. If a regexp has it, then the dot "." match literally any character.

There exist following character classes:

* \d: – digits.
* \D: – non-digits.
* \s: – space symbols, tabs, newlines.
* \S: – all but \s.
* \w: – English letters, digits, underscore '_'.
* \W: – all but \w.
any character if with the regexp 's' flag, otherwise any except a newline.

…But that’s not all! 

The Unicode encoding, used by JavaScript for strings, provides many properties for characters, like: which language the letter belongs to (if a letter) it is it a punctuation sign, etc.

Modern JavaScript allows to use these properties in regexps to look for characters, for instance:

* A cyrillic letter is: \p{Script=Cyrillic} or \p{sc=Cyrillic}.
* A dash (be it a small hyphen - or a long dash —): \p{Dash_Punctuation} or \p{pd}.
* A currency symbol, such as $, € or another: \p{Currency_Symbol} or \p{sc}.
…And much more. Unicode has a lot of character categories that we can select from.

The time has a format: hours:minutes. Both hours and minutes has two digits, like 09:00.
Make a regexp to find time in the string: Breakfast at 09:00 in the room 123:456.

const text = "Breakfast at 09:00 in the room 123:456.";
const timeRegex = /\b\d{2}:\d{2}\b/g;
const matches = text.match(timeRegex);

if (matches) {
    console.log("Found time(s):", matches);
} else {
    console.log("No matching time found.");
}

## Escaping, and Special Characters: 

To use a special character as a regular one, prepend it with a backslash: \..
That’s also called “escaping a character”.

* A slash:
A slash symbol '/' is not a special character, but in JavaScript it is used to open and close the regexp: /...pattern.../, so we should escape it too.

Here’s what a search for a slash '/' looks like:
alert( "/".match(/\//) ); // '/'

On the other hand, if we’re not using /.../, but create a regexp using new RegExp, then we don’t need to escape it:
alert( "/".match(new RegExp("/")) ); // '/'

* new RegExp:

If we are creating a regular expression with new RegExp, then we don’t have to escape /, but need to do some other escaping.

For instance, consider this:

let reg = new RegExp("\d\.\d");

alert( "Chapter 5.1".match(reg) ); // null

The search worked with /\d\.\d/, but with new RegExp("\d\.\d") it doesn’t work, why?

The reason is that backslashes are “consumed” by a string. Remember, regular strings have their own special characters like \n, and a backslash is used for escaping.


* To search special characters [ \ ^ $ . | ? * + ( ) literally, we need to prepend them with \ (“escape them”).
* We also need to escape / if we’re inside /.../ (but not inside new RegExp).
* When passing a string new RegExp, we need to double backslashes \\, cause strings consume one of them.

                                                                        --- END OF WEEK 2 ---

  # Week 3:

  ## Day 1:

  ### Node JS:
  As an asynchronous event driven JavaScript runtime, Node.js is designed to build scalable network applications.
  In the context of Node.js, blocking and non-blocking refer to the behavior of I/O operations (like reading from a file or making a network request) in the event-driven, 
  asynchronous, and non-blocking I/O paradigm that Node.js is built upon.
  * Blocking I/O:
In traditional synchronous programming, when an I/O operation is performed, the program execution is halted until the operation is completed. This means if you read a file or make a network request, the program will wait until the file is read or the request is completed before it can do anything else. This is called blocking I/O because it blocks the execution of the program until the I/O operation is finished.

const fs = require('fs');
const data = fs.readFileSync('file.txt'); // This is a blocking I/O operation
console.log(data.toString());
console.log('Program Ended');

* Non-blocking I/O:
In contrast, Node.js uses non-blocking I/O operations. When a non-blocking I/O operation is initiated, the program continues to execute the next line of code without waiting for the I/O operation to finish. A callback function is typically provided that gets executed once the I/O operation is completed. This approach allows Node.js to handle many connections concurrently.

const fs = require('fs');

fs.readFile('file.txt', (err, data) => { // This is a non-blocking I/O operation
    if (err) throw err;
    console.log(data.toString());
});

console.log('Program Ended');

In this example, the readFile function is non-blocking. It takes a callback function as an argument, which will be executed when the file reading operation is completed. The program continues executing the next line (console.log('Program Ended');) without waiting for the file reading operation to finish.
The non-blocking nature of Node.js is one of its key features, enabling highly scalable applications where many I/O operations can be handled simultaneously without blocking the execution flow. It's essential to understand this asynchronous behavior when working with Node.js applications.

Node.js takes the event model a bit further. It presents an event loop as a runtime construct instead of as a library. In other systems there is always a blocking call to start the event-loop. Typically behavior is defined through callbacks at the beginning of a script and at the end starts a server through a blocking call like EventMachine::run(). In Node.js there is no such start-the-event-loop call. Node.js simply enters the event loop after executing the input script. Node.js exits the event loop when there are no more callbacks to perform. This behavior is like browser JavaScript — the event loop is hidden from the user.

##  The "harmony" Runtime flag: 
The "Harmony" flag in the context of JavaScript refers to the set of experimental features and proposed ECMAScript (ES) standards that were in the process of being developed when ES6 (ECMAScript 2015) was under development.


The "Harmony" flag in the context of JavaScript refers to the set of experimental features and proposed ECMAScript (ES) standards that were in the process of being developed when ES6 (ECMAScript 2015) was under development.

In the early stages of development, many features that are now a standard part of JavaScript (such as arrow functions, classes, and modules) were marked as experimental and were not enabled by default in JavaScript engines to avoid breaking existing code. To experiment with these features, developers had to enable specific flags in JavaScript engines, and one of those flags was called the "Harmony" flag.

For example, in Node.js, you could enable experimental features using the --harmony flag when running your scripts. An example command might look like this:
node --harmony myscript.js


node.green website:  provides an excellent overview over supported ECMAScript features in various versions of Node.js, based on kangax's compat-table.

Summary: 

* 1. Node JS is constantly upgraded, meaning you can do a lot of cool things like creating your own web server, insisting on cool game features, and efficiency in JS code. Node.js is designed to build scalable network applications.
* 2. You use the "command line interface" to access it. That's where node.js files are indicated. eg. cmd, terminal, powershell etc. 
* 3.  We use it to create server-side applications. 
* 4. Modules in NodeJS are the same as JavaScript libraries.
* 5. NPM: Node Package Manager; helps install packages and libraries within NodeJS. 
* 6. It contains all the files you need, for a package. 




  




 



