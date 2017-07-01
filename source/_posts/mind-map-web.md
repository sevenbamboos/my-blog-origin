---
title: Mind Map for Web
date: 2017-07-01 17:09:56
category: Mind Map
---

Tips and references from articles about web programming.

<!-- more -->

Keywords | Reference
--- | ---
Webpack Gulp | [Ref][1]
module.exports exports | [Ref](#module-exports)

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

[1]: https://www.liquidlight.co.uk/blog/article/getting-started-with-webpack-3/