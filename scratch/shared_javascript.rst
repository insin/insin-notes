=================
Shared JavaScript
=================

.. highlight:: javascript

``module.exports`` Detection / Global Namespace Stuffing
========================================================

Thisn is what I'm currently do in my own shared modules - it's inelegant
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

Research
========

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

* http://caolanmcmahon.com/posts/writing_for_node_and_the_browser

* http://ender.no.de/
