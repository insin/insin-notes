======================================
Extending The Sphinx JavaScript Domain
======================================

Notes exploring changes which could be made to the `Sphinx`_ JavaScript domain
to take full account of JavaScript's capabilities and object model.

[TODOs/TBDs are expressed in square brackets]

.. _`Sphinx`: http://sphinx.pocoo.org

Current JavaScript Domain
=========================

The JavaScript domain (name **js**) currently provides the following
directives:

.. rst:directive:: .. js:function:: name(signature)

   Describes a JavaScript function or method.

.. rst:directive:: .. js:class:: name

   Describes a constructor that creates an object.

.. rst:directive:: .. js:attribute:: object.name

   Describes the attribute *name* of *object*.

.. rst:directive:: .. js:data:: name

   Describes a global variable or constant.

These roles are provided to refer to the described objects:

.. rst:role:: js:func
          js:class
          js:data
          js:attr

Terminology
===========

[Is there an set of single words, or two word phrases at a push, we
could agree to use to differentiate between properties of a constructor, a
prototype and an instance? The following are commonly used.]

class
   []
prototype
   []
variable
   []
object
   []
function
   []
method
   []
instance
   []
static
   []
extends
   []
inherits
   []

Necessary Capabilities
======================

What are the full range of properties a JavaScript constructor, instances
created from it and regular Objects can have?

.. code-block:: javascript

   var data = 0

   var object = {
     prop: 42
   , func: function() {}
   }

   function Constructor(arg1) {
     this.instanceProperty1 = arg1
     this.instanceProperty2 = Array.prototype.slice.call(arguments, 1)
     this.instanceFunction = function() {}
   }
   Constructor.constructorFunction = function() {}
   Constructor.constructorProperty = true
   Constructor.prototype.prototypeFunction = function() {}
   Constructor.prototype.prototypeProperty = 42

   var instance = new Constructor('steve', 1, 2, 3)

Proposed JavaScript Domain
==========================

.. rst:directive:: .. js:constructor:: name(signature)

   Describes a constructor that creates an object.

   The underlying implementation for :js:class:: is already called JSConstructor
    - keep js:class as an alias?

.. rst:directive:: .. js:prototype:: name

   Describes a constructor's prototype.

.. rst:directive:: .. js:object:: name

   An Object which contains... stuff.

.. rst:directive:: .. js:function:: name(signature)

   Describes a JavaScript function.

   When top level:

      name(signature)
         Top-level function
      constructor.name(signature)
         Constructor function -- "static"
      constructor.prototype.name(signature)
         Prototype function -- "method"
      object.name(signature)
         Object function -- "static"

   When nested under:

      constructor
         Prototype function -- "method"
      prototype
         Prototype function -- "method"
      object
         Object function -- "static"

   [What about functions attached directly to instances?]

.. rst:directive:: .. js:property:: name

   Describes a property of an object.

   [What does nesting mean?]

   [Constructor "static" property vs. prototype property vs. instance property]

[More...]
