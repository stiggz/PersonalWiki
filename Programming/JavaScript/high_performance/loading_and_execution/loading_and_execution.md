## Loading and Execution [Back](./../high_performance.md)

In fact, most browsers use a single process for both user interface (UI) updates and JavaScript execution, so only one can happen at any given moment in time. If we loaded JavaScript between a document, no matter using closed tag or `src` attribute, the browser has to block the user interaction to load and execute the script.

### Script Positioning

Traditionally, `<script>` tags that are used to load external JavaScript files have appeared in the `<head>`, along with `<link>` tags to load external CSS files and other metainformation(元信息s) about the page.

The theory was that it's best to **keep as many style and behavior dependencies together**, loading them first so that the page will come in looking and behaving correctly.

```html
<html>
    <head>
        <title>Script Example</title>
        
        <!-- Example of inefficient script positioning -->
        <script type="text/javascript" src="file1.js"></script>
        <script type="text/javascript" src="file2.js"></script>
        <script type="text/javascript" src="file3.js"></script>
        <link rel="stylesheet" type="text/css" href="style.css"></link>
    </head>
</html>
```

Because scripts block downloading of all resource types on the page, it's recommended **to place all `<script>` tags as close to the bottom of the `<body>` tag as possible** so as not to affect the download of the entire page.

```html
<html>
    <head>
        <title>Script Example</title>
        <link rel="stylesheet" type="text/css" href="style.css"></link>
    </head>
    <body>
        <p>Hello World!</p>
        
        <!-- Example of recommended script positioning -->
        <script type="text/javascript" src="file1.js"></script>
        <script type="text/javascript" src="file2.js"></script>
        <script type="text/javascript" src="file3.js"></script>
    </body>
</html>
```

### Grouping Scripts

Since each `<script>` tag blocks the page from rendering during initial download, it's helpful **to limit the total number of `<script>` tags** contained in the page. This applies to both inline scripts as well as those in external files. That's **to reduce the delay while the code is executed**.

> **Never putting an inline script after a `<link>` tag**, because Steve Souders has found that an inline script placed after a `<link>` tag referencing an external stylesheet caused the browser to block while waiting for the stylesheet to download.

Therefore, if a large website or web application has several required JavaScript files, you can use a build tool to concatenate these files together into a single file and import it. 

Yahoo! User Interface (YUI) has given you a way to pull in any nubmer of JavaScrpit files by using **a combo-handled URL** like: http://yui.yahooapis.com/combo?2.7.0/build/yahoo/yahoo-min.js&2.7.0/build/event/event-min.js

### Nonblocking Scripts

The secret to nonblocking scripts is to load the JavaScript source code after the page has finished loading. In technical terms, this means downloading the code after the `load` event of the `window` object has been triggered.

#### Deferred Scripts

HTML 4 defines an additional attribute for the `<script>` tag called `defer`. The `defer` attribute indicates that the script contained within the element is not going to modify the DOM and therefore execution can be safely deferred until a later point in time.

```html
<script type="text/javascript" src="file1.js" defer></scirpt>
```

A `<script>` tag with `defer` may be placed anywhere in the document. The JavaScript file will begin downloading at the point that the `<script>` tag is parsed, but the code will not be executed until the DOM has been completely loaded. (before the `onload` event handler is called).

```html
<html>
    <head>
        <title>Script Defer Example</title>
    </head>
    <body>
        <script defer>
            alert("defer");
        </script>
        <script>
            alert("script");
        </script>
        <script>
            window.onload = function () {
                alert("load");
            };
        </script>
    </body>
</html>
```

Here is an example shown above. In browsers that don't support `defer`, the order of the alerts is "defer", "script", and "load", while in browsers that support `defer`, the order of the alerts is "script", "defer", and "load". 

#### Dynamic Script Elements

At the root of a document, the `<script>` element isn't any different than any other element on a page: references can be retrieved through the DOM, and they can be moved, removed from the document, and even created. That's why we can easiy using standard DOM methods to create a `<script>` element.

```js
var script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'file1.js';

document.getElementsByTagName('head')[0].appendChild(script);
```

> It's generally safer to add new `<script>` nodes to the `<head>` element instead of the `<body>`, especially if this code is executing during page load. That's because, Internet Explorer may experience an "operation aborted" error if all of the `<body>` contents have not yet been loaded.

Because there will be problems if the code contains only interfaces to be used by other scripts, we need to track when the coe has been fully downloaded and ready for use. In Firefox, Opera, Chrome and Safari 3+, we can use `load` event to check this:

```js
var script = document.createElement('script');
script.type = 'text/javascript';

/** Firefox, Opera, Chrome, Safari 3+ */
script.onload = function () {
    alert('Script Loaded!');
};

script.src = 'file1.js';

document.getElementsByTagName('head')[0].appendChild(script);
```

While in Internet Explorer, we can use `readystatechange` event to check the property `readyState` of a `<script>` element. There're 6 possible values for `readyState`:

- **uninitialized**: The default state
- **loading**: Download has begun
- **loaded**: Download has completed
- **interactive**: Data is completely downloaded but isnot fully availabe
- **complete**: All data is ready to be used