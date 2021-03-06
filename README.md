# Delayed

**A collection of JavaScript helper functions for your functions, using `setTimeout()` to delay, defer and debounce**

[![NPM](https://nodei.co/npm/delayed.svg)](https://nodei.co/npm/delayed/)

**Delayed** is designed for use across JavaScript runtimes, including the browser and within Node.js. It is available in npm as *"delayed"* or can be [downloaded](https://raw.github.com/rvagg/delayed/master/delayed.js).

## API

 * [`delay()`](#delay)
 * [`defer()`](#defer)
 * [`delayed()`](#delayed)
 * [`deferred()`](#deferred)
 * [`debounce()`](#debounce) *(a.k.a `cumulativeDelayed()`)*
 * [`noConflict()`](#noConflict)

---------------------------------------------

<a name="delay"></a>
### `delay(fn, ms)`<br/>`delay(fn, ms, context)`<br/>`delay(fn, ms, context, arg1, arg2...)`

`delay()` is an interface to `setTimeout()` but with better `this` handling and consistent cross-browser argument passing.

Example:

```js
// print "beep" to the console after 1/2 a second
delayed.delay(() => console.log('beep'), 500)

function print (a, b) { console.log(this[a], this[b]) }
delayed.delay(print, 5000, { 'foo': 'Hello', 'bar': 'world' }, 'foo', 'bar')
// after 5 seconds, `print` is executed with the 3rd argument as `this`
// and the 4th and 5th as the arguments
```

`delay()` returns the timer reference from `setTimeout()` so it's possible to retain it and call `clearTimeout(timer)` to cancel execution.

---------------------------------------------

<a name="defer"></a>
### `defer(fn)`<br/>`defer(fn, context)`<br/>`defer(fn, context, arg1, arg2...)`

`defer()` is essentially a shortcut for `delay(fn, 1...)`, which achieves a similar effect to `process.nextTick()` in Node.js or the proposed `setImmediate()` that we should start seeing in browsers soon (it exists in IE10). Use it to put off execution until the next time the browser/environment is ready to execute JavaScript. Given differences in timer resolutions across browsers, the exact timing will vary.

*Note: future versions of **delayed** will likely detect for and use `setImmediate()` for deferred functions.*

`defer()` returns the timer reference from `setTimeout()` so it's possible to retain it and call `clearTimeout(timer)` to cancel execution, as long as it's done within the same execution *tick*.

---------------------------------------------

<a name="delayed"></a>
### `delayed(fn, ms)`<br/>`delayed(fn, ms, context)`<br/>`delayed(fn, ms, context, arg1, arg2...)`

Returns a new function that will delay execution of the original function for the specified number of milliseconds when called.

Example:

```js
// a new function that will print "beep" to the console after 1/2 a second when called
var delayedBeeper = delayed.delay(() => console.log('beep'), 500)

delayedBeeper()
delayedBeeper()
delayedBeeper()

// 1/2 a second later we should see:
//   beep
//   beep
//   beep
// each will have executed on a different timer
```

The new delayed function will return the timer reference from `setTimeout()` so it's possible to retain it and call `clearTimeout(timer)` to cancel execution *of that particular call*.

---------------------------------------------

<a name="deferred"></a>
### `deferred(fn)`<br/>`deferred(fn, context)`<br/>`deferred(fn, context, arg1, arg2...)`

Returns a new function that will defer execution of the original function, in the same manner that `defer()` defers execution.

The new delaying function will return the timer reference from `setTimeout()` so it's possible to retain it and call `clearTimeout(timer)` to cancel execution *of that particular call*, as long as it's done within the same execution *tick*.

---------------------------------------------

<a name="debounce"></a>
### `debounce(fn, ms)`<br/>`debounce(fn, ms, context)`<br/>`debounce(fn, ms, context, arg1, arg2...)`

_Also available with the name `cumulativeDelayed()`_

Returns a new function that will delay execution of the original function for the specified number of milliseconds when called. Execution will be **further delayed** for the same number of milliseconds upon each subsequent call before execution occurs.

The best way to explain this is to show its most obvious use-case: keyboard events in the browser.

```html
<!doctype html>
<html>
<body>
<textarea id="input" style="width: 100%; height: 250px;">Type in here</textarea>
<div id="output" style="border: solid 1px black;">Show in here</div>

<script src="https://raw.github.com/rvagg/delayed/master/delayed.js" type="text/javascript"></script>
<script>
  function render (event) {
    var content = event.target.value
    content = content.replace('&', '&amp;')
                     .replace('>', '&gt;')
                     .replace('<', '&lt;')
                     .replace('\n', '<br>')
    document.getElementById('output').innerHTML = content
  }

  var delayedRender = delayed.debounce(render, 500)

  document.getElementById('input').addEventListener('keyup', delayedRender)
</script>
</body>
</html>
```

`debounce()` is a way of putting off tasks that need to occur in reaction to potentially repeating events, particularly where the task may be expensive or require some time to execute such as an AJAX call.

In our example, we're reacting to keyboard events but instead of running the `render()` function each time a key is pressed, we keep on pushing back execution while keyboard events keep coming in. Only when we have a pause in keyboard events of at least 500ms does `render()` actually get called.

The new delaying function will return the timer reference from `setTimeout()` so it's possible to retain it and call `clearTimeout(timer)` to cancel execution *of that particular call*.

---------------------------------------------

## Licence & Copyright

*Delayed* is Copyright (c) 2014 Rod Vagg <@rvagg> and licensed under the MIT licence. All rights not explicitly granted in the MIT license are reserved. See the included LICENSE.md file for more details.
