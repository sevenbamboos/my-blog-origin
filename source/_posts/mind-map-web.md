---
title: Mind Map of Web
date: 2017-07-01 17:09:56
category: Mind Map
---

Tips and references from articles about web programming. It's called mind map not because of the mind map drawing, but due to the similarity of the data structure "map". Given a keyword, I can find the read articles within a constant time (O(1)), just as `map.for(key:) -> <Value?>`.

# Map

Keywords | Comment & Reference
--- | ---
Webpack Gulp | [Webpack 3 with Gulp][1]
module.exports exports | [Comment](#module-exports)
MVC MVP MVVM | [Comment](#mvp-mvvm), [iOS architecture][2], [MVVM + Redux][3], [mvc and cocoa][4], [mvc/mvp/mvvm][5]
flatMap | [Comment](#flatMap) [flatMap][7]
await async Promise | [Comment](#await-async-Promise), [learn about promises][6]
put post | [Comment](#put-post), [RESTful cookbook][8]
event bubbling | [Comment](#event-bubbling), [Event Propagation Explained][9]
console.log | [Comment](#console-log), [Javascript console][10]
partial curry high order Lodash | [Comment](#high-order-lodash), [Higher order in Lodash][11]

<!-- more -->

# high order lodash
partial returns a function that binds rest of arguments to its first argument.
```
function partial_impl(fn, ...args) {
  return function(...newArgs) {
    fn.apply(this, args.concat(newArgs));
    // fn.call needs a series of arguments, while apply can be used with an array-like argument
  }
}

// lodash's built-in partial supports to bind argument to a specific position
_.partial(fn, _, param); 
```

curry returns a function that supports to bind argument one at a time
```
// provide a second parameter to specify the argument length to help eliminate the confusion of optional argument
let newDivide = _.curry(divide);
let half = newDivide(_, 2);
half(10); // 5
```

flow combines an array of functions into one like `_.flow([k, g, f])(x) <=> k(g(f(x)))`

# console log
```
// %i, %d for integer, %f for float
console.log('string %s', 'substitutions');

// %c for CSS style
const success = [
 'background: green', 'color: white', 'display: block', 'text-align: center'
].join(';');
console.info('%c /dancing/bears was Successful!', success);

// log as error with a stack trace if not fulfilled
console.assert(isTrue, 'This will not');

// format as table
console.table([value1, value2]);
console.table({key1: value1, key2: value2});

// performance
console.time('id for timer');
console.timeEnd('id for timer');
```

# event bubbling
Capture phase is from the window to the event target parent.
```
// The third parameter is optional. 
// By default it's false so that `listener` won't be a capture.
element.addEventListener('click', listener, true)
```

Then the event arrives at itself. Note that some events e.g. focus, blur and load stop at this phase. 

Bubble phase is from the event target parent back to the window.

Regarding event, it can always get from `event.target`, while `event.currentTarget` points to the current element that the event is arrives. `event.stopPropagation()` will stop the propagation. `event.preventDefault()` will prevent some default behavior that browser registers for certain UI elements. For example, clicking a link is supposed to navigate to a new page and clicking a submit button will submit the form.

# put post
If client knows where to put the (new) resource, use put:
```
PUT /article/1234 HTTP/1.1
<article/>
```

Otherwise, client uses post:
```
POST /articles HTTP/1.1
<article/>

HTTP/1.1 201 Created
Location: /articles/63636
```
And the location of the (created) resource will be included in the response.

# flatMap
flatMap can return zero or one or multiple items when applying function on each map item. Return empty array means ignore.
 
# await async Promise
Note that the last "downloadFiles" example. await accept a Promise and async function returns a Promise.

# module exports
module.exports points to the same reference as exports.
```
// module-a
class Foo {...}
module.exports = Foo;

// module-b
function bar() {...}
exports.bar = bar;

// app.js 
var aFoo = require('./module-a');
aFoo.method(...);

var moduleB = require('./module-b');
moduleB.bar(...);
``` 

# mvp mvvm
The difference between MVP and MVVM is the data binding. VM will create the binding and later on the model and view will be in sync without the interference from VM.

For iOS, UIViewController is actually a presenter, a supervising controller and therefore iOS's MVC is MVP

[1]: https://www.liquidlight.co.uk/blog/article/getting-started-with-webpack-3/
[2]: https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52
[3]: https://medium.com/monitisemea/using-redux-with-mvvm-on-ios-18212454d676
[4]: https://www.cocoawithlove.com/blog/mvc-and-cocoa.html
[5]: https://juejin.im/post/593021272f301e0058273468 
[6]: https://medium.com/@bluepnume/learn-about-promises-before-you-start-using-async-await-eb148164a9c8
[7]: http://2ality.com/2017/04/flatmap.html 
[8]: http://restcookbook.com/HTTP%20Methods/put-vs-post/ 
[9]: https://www.sitepoint.com/event-bubbling-javascript/ 
[10]: https://medium.freecodecamp.org/how-to-get-the-most-out-of-the-javascript-console-b57ca9db3e6d 
[11]: https://blog.pragmatists.com/higher-order-functions-in-lodash-3283b7625175 
