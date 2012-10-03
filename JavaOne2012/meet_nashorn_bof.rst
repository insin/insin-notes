=====================================
Meet The Nashorn Development Team BOF
=====================================

* URL: https://oracleus.activeevents.com/connect/sessionDetail.ww?SESSION_ID=4763

Notes
=====

* No plans to open source Node.jar
* 3 former Rhino developers on the Nashorn team
* ...don't have notes for the questions I asked, so imagine you're reading
  interesting implementeation details here...
* Didn't look at other JavaScript implementations during development to keep it
  "pure"
* No optimisations are planned for the near future.
* New features
  * Here strings
  * Edit strings
  * Shebangs
* Compatibility script for Rhino code
* Making *heavy* use of invokeDynamic
* Node implementation is not as fast as native Node, but already in the ballpark
  and they think they can get there \**Akhil sinks into his chair in the
  audience :)*\*
* 1 test left to reach 100% conformance with ECMA-262, ~8 bugs
* Not rushing it out the door - "we have time to get it right"
* Will be able to run under one of the smaller Jigsaw runtimes.
* Primary purpose is to enable scripting for Java, not to be some sort of
  solution for JavaScript in general.