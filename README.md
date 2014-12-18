# Browserify 7 Bug #1020 Repro

This repository contains a repro of bug [#1020] for Browserify 7 and an example
of how it used to work in Browserify 4.

## Bug

Browserify 7 does not support referencing an external file, e.g. `hello.js`, as
`--require` as well as inside the entry file. The Browserify 7 `bundle.js` has
an `undefined` reference to `hello.js`, see below:

```
# Does not work in Browserify 7 if `hello.js` is also referenced in `main.js`:
browserify --require ../hello.js:hello ../main.js > bundle.js
```

## Setup

```bash
npm run setup
npm run build
open 4.x/index.html
open 7.x/index.html
```

## Browserify 4 `bundle.js`

```javascript
require=(function e(t,n,r){function s(o,u){if(!n[o]){if(!t[o]){var a=typeof require=="function"&&require;if(!u&&a)return a(o,!0);if(i)return i(o,!0);throw new Error("Cannot find module '"+o+"'")}var f=n[o]={exports:{}};t[o][0].call(f.exports,function(e){var n=t[o][1][e];return s(n?n:e)},f,f.exports,e,t,n,r)}return n[o].exports}var i=typeof require=="function"&&require;for(var o=0;o<r.length;o++)s(r[o]);return s})({"ZyvcgE":[function(require,module,exports){
module.exports = function hello() {
  return 'Hello.';
};

},{}],"hello":[function(require,module,exports){
module.exports=require('ZyvcgE');
},{}],3:[function(require,module,exports){
var hello = require('./hello');

console.log('Main: hello:', hello());

},{"./hello":"ZyvcgE"}]},{},[3])
```

## Browserify 7 `bundle.js`

```javascript
require=(function e(t,n,r){function s(o,u){if(!n[o]){if(!t[o]){var a=typeof require=="function"&&require;if(!u&&a)return a(o,!0);if(i)return i(o,!0);var f=new Error("Cannot find module '"+o+"'");throw f.code="MODULE_NOT_FOUND",f}var l=n[o]={exports:{}};t[o][0].call(l.exports,function(e){var n=t[o][1][e];return s(n?n:e)},l,l.exports,e,t,n,r)}return n[o].exports}var i=typeof require=="function"&&require;for(var o=0;o<r.length;o++)s(r[o]);return s})({1:[function(require,module,exports){
var hello = require('./hello');

console.log('Main: hello:', hello());

},{"./hello":undefined}],"hello":[function(require,module,exports){
//           ^^^^^^^^^ Bug: Should not be `undefined`!
module.exports = function hello() {
  return 'Hello.';
};

},{}]},{},[1]);

```

[#1020]: https://github.com/substack/node-browserify/issues/1020
