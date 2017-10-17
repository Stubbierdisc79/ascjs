# ascjs

[![License: ISC](https://img.shields.io/badge/License-ISC-yellow.svg)](https://opensource.org/licenses/ISC) [![Build Status](https://travis-ci.org/WebReflection/ascjs.svg?branch=master)](https://travis-ci.org/WebReflection/ascjs) [![Coverage Status](https://coveralls.io/repos/github/WebReflection/ascjs/badge.svg?branch=master)](https://coveralls.io/github/WebReflection/ascjs?branch=master) [![donate](https://img.shields.io/badge/$-donate-ff69b4.svg?maxAge=2592000&style=flat)](https://github.com/WebReflection/donate)

ES2015 to CommonJS import/export transformer

- - -

This module does one thing only:
it loosely transpiles **ES2015** [import](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import)/[export](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export) statements **into** valid **CommonJS** in order to fix the only part of Node that's incompatible with modern JS.

### Features

  * extremely lightweight, based on [cherow](https://github.com/cherow/cherow) for performance and reliability, it transforms only imports/exports ignoring everything else
  * produces modern JavaScript, you are in charge of extra transformations if needed
  * indentation, spaces, semi or no-semi are preserved: beautiful source code remains beautiful
  * uses same [Babel](http://babeljs.io) convention, resolving `export default ...` intent as `exports.default`
  * you can finally write `.js` code and transform it for Node only before publishing on _npm_
  * you could write `.mjs` modules and transform them into CommonJS for [Browserify](http://browserify.org) or other bundlers as target

### Constrains

  * live bindings for exported values are not preserved. You need to delegate in scope eventual changes
  * dynamic `import(...)` is untouched. If you write that, let [Webpack](https://webpack.js.org) handle it for you later on
  * there is no magic whatsoever in module names resolution, what you write in ESM is what you get as CJS

### Example
This module can transform the following ES2015+ code
```js
import func, {a, b} from './module.js';
import * as tmp from 'other';

const val = 123;

export default function test() {
  console.log('ascjs');
};

export {func, val};
```
into the following one:
```js
const func = (m => m.__esModule ? m : {default: m})(require('./module.js')).default;
const {a, b} = func;
const tmp = require('other');

const val = 123;

function test() {
  console.log('ascjs');
}
Object.defineProperty(exports, '__esModule', {value: true}).default = test;

exports.func = func;
exports.val = val;
```