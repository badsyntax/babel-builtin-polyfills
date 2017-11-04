# Babel 7 Builtin polyfills

Babel can provide polyfills for your env but also only provide polyfills that are _actually used_. This is pretty awesome as it can 
reduce your bundle file size.

This can be achieved with the options `"useBuiltIns": "usage"` for the `@babel/env` preset. 

Additionally, we can use the `transform-runtime` plugin to transform polyfills to the local module scope, to avoid potential 
global conflicts. This will not work for instance methods though, eg `String.prototype.includes`, and so Babel will provide 
global polyfills for those.

Additionally, now that Babel can provide polyfills based on usage, we want to avoid using Babel helpers. For example, we can 
configure the `transform-object-rest-spread` plugin to use builtIns: `"useBuiltIns": true`. The results in the plugin using 
`Object.assign` instead of `_extends` (and then `Object.assign` might be pollyfilled based on your env).

Unfortunately I haven't found a nice solution for polyfilling browser/DOM specific features like `fetch`.

Input:

```
class Foo {}
const bar = () => {};
const a = [...['a', 'b', 'c'], 'foo'];
const b = { a: 'b', ...{ a: 'c' } };
fetch();
new Promise();
new Set();
new Map();
''.includes('');
[].includes('');
```


Output:

```
$ ./node_modules/.bin/babel index.js
@babel/preset-env: `DEBUG` option

Using targets:
{
  "ie": "8"
}

Using modules transform: false

Using plugins:
  check-constants { "ie":"8" }
  transform-arrow-functions { "ie":"8" }
  transform-block-scoped-functions { "ie":"8" }
  transform-block-scoping { "ie":"8" }
  transform-classes { "ie":"8" }
  transform-computed-properties { "ie":"8" }
  transform-destructuring { "ie":"8" }
  transform-duplicate-keys { "ie":"8" }
  transform-for-of { "ie":"8" }
  transform-function-name { "ie":"8" }
  transform-literals { "ie":"8" }
  transform-object-super { "ie":"8" }
  transform-parameters { "ie":"8" }
  transform-shorthand-properties { "ie":"8" }
  transform-spread { "ie":"8" }
  transform-sticky-regex { "ie":"8" }
  transform-template-literals { "ie":"8" }
  transform-typeof-symbol { "ie":"8" }
  transform-unicode-regex { "ie":"8" }
  transform-new-target { "ie":"8" }
  transform-regenerator { "ie":"8" }
  transform-exponentiation-operator { "ie":"8" }
  transform-async-to-generator { "ie":"8" }

Using polyfills with `usage` option:

[index.js] Added following polyfills:
  es6.string.includes { "ie":"8" }
  es7.array.includes { "ie":"8" }
import "@babel/polyfill/lib/core-js/modules/es7.array.includes";
import "@babel/polyfill/lib/core-js/modules/es6.string.includes";

var _Map = require("babel-runtime/core-js/map");

var _Set = require("babel-runtime/core-js/set");

var _Promise = require("babel-runtime/core-js/promise");

var _Object$assign = require("babel-runtime/core-js/object/assign");

var _classCallCheck = require("babel-runtime/helpers/classCallCheck");

var Foo = function Foo() {
  _classCallCheck(this, Foo);
};

var bar = function bar() {};

var a = ['a', 'b', 'c'].concat(['foo']);

var b = _Object$assign({
  a: 'b'
}, {
  a: 'c'
});

fetch();
new _Promise();
new _Set();
new _Map();
''.includes('');
[].includes('');
```

## create-react-app

At the moment `create-react-app` uses the [babel-preset-react-app](https://github.com/facebookincubator/create-react-app/tree/master/packages/babel-preset-react-app) preset which uses `babel-preset-env@1.6.1` which does not support this `usage` feature, 
which was added with [2.0](https://github.com/babel/babel-preset-env/pull/241).
