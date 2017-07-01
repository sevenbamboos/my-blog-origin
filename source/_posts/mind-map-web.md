---
title: Mind Map for Web
date: 2017-07-01 17:09:56
category: Mind Map
---

Tips and references from articles about web programming. It's called mind map not because of the mind map drawing, but due to the similarity of the data structure "map". Given a keyword, I can find the read articles within a constant time (O(1)), just as `map.for(key:) -> <Value?>`.

<!-- more -->

Keywords | Reference
--- | ---
Webpack Gulp | [Webpack 3 with Gulp][1]
module.exports exports | [Comment](#module-exports)
MVC MVP MVVM | [iOS architecture][2], [MVVM + Redux][3], [Comment](#mvp-mvvm), [mvc and cocoa][4], [mvc/mvp/mvvm][5]
flatMap | [Comment](#flatMap) [flatMap][7]
await async Promise | [Comment](#await-async-Promise), [learn about promises][6]
put post | [Comment](#put-post), [RESTful cookbook][8]

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
