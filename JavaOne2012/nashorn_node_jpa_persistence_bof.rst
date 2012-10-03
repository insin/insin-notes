==========================================
Nashorn, Node.jar and Java Persistence BOF
==========================================

* URL: https://oracleus.activeevents.com/connect/sessionDetail.ww?SESSION_ID=6661

Node.jar, Akhil Arora
=====================

* Nashorn
* Implemented Node's evented APIs
* Using the Grizzly Framework under the hood (GlassFish)
* Can run on any Nashorn platform

  * Including Java SE Embedded

* Node port will always be single-threaded/evented, but Jave part can take
  advantage of threads/multi-core
* Only on Java SE 7 and above
* JavaScript part is single-threaded, but Java portions can spin up as many as
  you want. Have to post to the event queue to communicate back to Node.
* Access to BigDecimal/BigInteger on JVM (and others etc. etc.)
* Mapping from Node modules to Java APIs used in their implementation
  (incomplete list)

  =============  ===================================
  buffer         Grizzly ByteBuffer (nio ByteBuffer)
  child_process  ProcessBuilder
  dns            InetAddress etc.
  fs             nio.File
  http(s)        Grizzly
  stream         Grizzly nio Stream
  =============  ===================================

Development
-----------

* WIP based on Node v0.8.2
* Reusing Node (JavaScript) source as much as possible
* Redirecting to Java APIs where required in _wrap implementation modules
* Using the Node.js unit tests to test
* Lists of modules which were "Mostly Working", "Partial", "TBD", I only got
  these ones down:

  TBD
     crypto, cluster, dns, readline, repl, tty, zlib
  Partial
     fs, http, net, os

Demos
-----

Running on a Raspberry PI

* java -jar node-0.1.jar fortune.js

  * Serving up a ``fortune`` entry

* dates.js

  * Writing a date to the response every second, using Java Date

Q&A
---

* Speed?

  * Not getting into it vs Mozilla and Google engines, but faster and smaller
    than Rhino already.

* Can Node modules retrieved via ``npm`` be used?

  * Yes, but can't load modules which require native extensions

* Are there any plans to provide wrap modules for popular modules which have
  native deps, like Socket.IO?

  * Socket.IO support will be an add-on (i.e. later), wrapping Grizzly
    implementations.

* Why, and why use JavaScript at all?

  * Node API is compact and powerful, with smaller code size
  * JavaScript is dynamic, reactive, fun!
  * Make use of Java APIs
  * Secured with Java security

JPA & Node.jar, Doug Clarke
===========================

Implementation details and JS code samples of 4 ways to use JPA persistence with
Nashorn/Node.jar.

Wrapped JPA, XML + annotated Java
---------------------------------

* Access Java EE with SSJS
* Node wrapped JPA
* Annotated Java classes and XML config file
* app.js & jpa.js

Wrapped dynamic JPA
-------------------

* Access Java EE with SSJS without Java
* No Java source classes
* Persistence XML
* EclipseLink offers dynamic persistence, spinning up classes at runtime
  (``access="VIRTUAL"``)

::

   +----------------------+ +-------+ +-------+
   |                      | |       | |       |
   |                      | |       | |       |
   |        Node.jar      | |Nashorn| |Eclipse|
   |                      | |       | |Link   |
   | +-------+  +-------+ | |       | |       |
   | |       |  |       | | |       | |       |
   | | app.js|  | jpa.js| | |       | |       |
   | |       |  |       | | |       | |       |
   | +-------+  +-------+ | |       | |       |
   +----------------------+ +-------+ +-------+

JS JPA & JS/JSON Config
-----------------------

* JavaScript object literals equivalent to XML config
* Factories required for creation of new objects, still early days on this
  front.

::

   +----------------------+ +-------+
   |                      | |       |
   |                      | |       |
   |        Node.jar      | |Nashorn|
   |                      | |       |
   | +-------+  +-------+ | |       |
   | |       |  |       | | |       |
   | | app.js|  | jpa.js| | |       |
   | |       |  |       | | |       |
   | +-------+  +-------+ | |       |
   +----------------------+ +-------+

JS Database JPA
---------------

* Types and mappings from DB
* Types creted based on the schema
* Names mapped to database table names

* Use case for Nashorn/Node could be scripted service chunks leveraging EE
  capabilities.

Q&A
---

* (Paraphrasing) Sample JS code is synchronous, WTF?

  * Code put together to demonstrate use, not a JS API
  * EntityManager can transparently be made asynchronous to support writing in
    the standard Node async way.

* (Adam Bien) Can we do annotations?

  * Not part of the JS language

* After this question there was a bit of discussion back and forth about ways
  you could annotate, use cases for using EE components in JS, but it's clear
  that it's possible to use chunks of EE with Node.jar and all the specifics are
  up for grabs right now.

* It sounds like it's hard to even get a hold of Node.jar if you work for Oracle,
  and there are no plans to open source it.

* Which is a pity, because more people who know Node.js and use EE could be of
  use here. I know I'd like to start playing with it yesterday!
