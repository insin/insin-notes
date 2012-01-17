=================
Shared JavaScript
=================

.. highlight:: javascript

``module.exports`` Detection / Global Namespace Stuffing
========================================================

This is what I'm currently do in my own shared modules - it's inelegant
and I suspect makes it hard to share tests between Node.js and browsers
in anything but node-qunit, but I'm yet to confirm that by trying to port
any of the modules which use this pattern over to Mocha for testing.

Pros:

* Simple.

Cons:

* Ugly.
* Boilerplated.
* Requires dependents to ``require()`` into a specifically-named variable.

*lib/<modulename>.js*::

   ;(function(__global__, server) {

   var dep = server ? require('dep') : __global__.dep

   var api {}

   // Define API

   if (server) {
      module.exports = api
   }
   else {
      __global__.<modulename> = API
   }

   })(this, !!(module && typeof module.exports == 'function'))

Bundling Dependencies
---------------------

Haven't actually tried this yet, but it's what I'd planned to do, calling
in the context of an Object which will receive exports.

*<modulename>.js*::

   ;(function() {

   var __global__ = {}

   ;(function() {

   // Source for namespace-stuffed dependencies

   // Source for module who needs the above dependencies

   }).call(__global__)

   window.modulename = __global__.modulename

   })()

Target End State
================

* Code everything using Node.js-style ``require()``.
* For isomorphic code which needs Node.js-only features, detect ``process``, or
  a similar Node.js global.
* Hack in Node shims only as required.
* Export for browsers:

  * Implement ``require()`` for use in an IIFE, not available publicly - the
    final API will still be exported as a property of ``window``.
  * Wrap code and dependencies with IIFEs which define ``module``, ``exports``
    and require.
* Dirt simple - *not automated* - should be driven off a file telling it exactly
  what to do. See Research section below for that. If you find yourself going
  down or needing to go down that route, use one of those instead.

Simple test case:

/concur.js::

   var is = require('isomorph/is')

   function extend..
   function mixin...
   function inheritPrototype...
   function inheritFrom...

   var Concur = exports.Concur = function...
   Concur.extendConstructor = function...

/node_modules/isomorph/is.js::

   function isArray...
   function isBoolean...
   function isDate...
   function isError...
   function isFunction...
   function isNumber...
   function isObject...
   function isRegExp...
   function isString...
   function isEmpty...

   module.exports = {
     Array: isArray
   , Boolean: isBoolean
   , Date: isDate
   , Empty: isEmpty
   , Error: isError
   , Function: isFunction
   , NaN: isNaN
   , Number: isNumber
   , Object: isObject
   , RegExp: isRegExp
   , String: isString
   }

Expected output (not tested, rough guesses)::

   ;(function() {
     var modules = {}
     // Naive much?
     function require(name) {
       return modules[name]
     }
     // Doesn't handle exports = blah
     function defineModule(name, fn) {
       var module = {}
         , exports = {}
       module.exports = exports
       fn(module, exports, require)
       modules[path] = module.exports
     }

     defineModule('isomorph/is', function(module, exports, require) {
   function isArray...
   function isBoolean...
   function isDate...
   function isError...
   function isFunction...
   function isNumber...
   function isObject...
   function isRegExp...
   function isString...
   function isEmpty...
   module.exports = {
     Array: isArray
   , Boolean: isBoolean
   , Date: isDate
   , Empty: isEmpty
   , Error: isError
   , Function: isFunction
   , NaN: isNaN
   , Number: isNumber
   , Object: isObject
   , RegExp: isRegExp
   , String: isString
   }
     })

     defineModule('concur', function(module, exports, require) {
   var is = require('isomorph/is')

   function extend..
   function mixin...
   function inheritPrototype...
   function inheritFrom...

   var Concur = exports.Concur = function...
   Concur.extendConstructor = function...
     })

     window['concur'] = require('concur')
   })

Research
========

* https://github.com/substack/node-browserify of course!

* https://github.com/visionmedia/mocha/tree/master/support

  Mocha has a build script which provides a browser-side ``require()`` and
  scans code being bundled for fixups for the browser, such as pointing at
  at stubs and shims for Node modules (which are kept in the */browser/*
  dir) and modifying inheritance to work cross-browser.

  It registers each file being bundled with its custom ``require`` by
  filename, wrapping it in a function which provides ``module``, ``exports``
  and ``require`` variables for the module to use.

  Any browser-specific setup is performed after the module is required into
  a global variable on ``window``.

  ``require()`` is a refactored version of:

  * https://github.com/weepy/brequire/blob/master/browser/require.js

* https://github.com/azer/onejs - "transform commonjs packages into single,
  stand-alone javascript files"

* https://github.com/medikoo/modules-webmake

* http://caolanmcmahon.com/posts/writing_for_node_and_the_browser

* http://ender.no.de/
