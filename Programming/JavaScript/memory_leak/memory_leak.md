## Memory Leak (內存泄漏) [Back](./../JavaScript.md)

In the process of developing front-end application, like web application, "memory leak" means that a space created before **cannot be used or collect any more** until the browser is closed.

Though the browser has GC (Garbage Collection), but there is bug on this collection, which will lead to memory leak case.

Here we list some cases that will cause memory leak in JavaScript:

### Event Listener

When a DOM has changed, if its event listener has not be removed, IE will not handle it for you and it causes memory leak.

```html
<div id="myDiv">
    <input type="button" value="Click me" id="myBtn">
</div>

<script type="text/javascript">
    var btn = document.getElementById('myBtn');
    btn.onclick = function () {
        /**
         * the div with id myDiv will be changed into a text,
         * but its click event listener still exsists.
         */
        document.getElementById('myDiv').innerHTML = 'Processing...';
    };
</script>
```

In such kinds of cases, you can solve by remove the listner:

```html
<div id="myDiv">
    <input type="button" value="Click me" id="myBtn">
</div>

<script type="text/javascript">
    var btn = document.getElementById('myBtn');
    btn.onclick = function () {
        /** clear listner */
        btn.onclick = null;
        
        document.getElementById('myDiv').innerHTML = 'Processing...';
    };
</script>
```

Or use event delegation(事件委託):

```html
<div id="myDiv">
    <input type="button" value="Click me" id="myBtn">
</div>

<script type="text/javascript">
    var btn = document.getElementById('myBtn');
    btn.onclick = function (event) {
        /** event delegation */
        event = event || window.event;
        
        if (event.target.id === 'myBtn') {
            document.getElementById('myDiv').innerHTML = 'Processing...';
        }
    };
</script>
```

### DOM or ActiveX Object Reference

In ECMAScript, reference between two DOM elements can be collected, if no other elements refer to them like:

```js
var a = document.getElementById('xxx');
var b = document.getElementById('xx');

/** can still be collected */
a.reference = b;
b.reference = a;
```

However, in IE, if you loop to refer to any DOM or ActiveX Object, GC won't collect and release these elements.

```js
var a = document.getElementById('xxx');
a.reference = a;
```

### Closure

Closures will sometimes cause memory leak by holding a variable.

```js
function bindEvent() {
    var obj = document.getElementById('xxx');
    
    obj.onclick = function () {
        /** will keep holding obj even if it's a empty function */
    };
}
```