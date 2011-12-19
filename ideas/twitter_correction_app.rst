=======================
Twitter App: Substitute
=======================

A `Twitter`_ app for conveniently correcting typos.

In lieu of an edit button, correcting a typo on Twitter involves deleting the
original tweet and creating a new one. If you're tweeting via the web interface,
this is annoying. If you're tweeting via SMS or via a browser on a standard
mobile phone using `m.twitter.com`_, this is more painful (first-world problems
definition) than it needs to be, as the typo *must* die.

.. _`Twitter`: http://twitter.com
.. _`m.twitter.com`: http://m.twitter.com

Specification
=============

The app should monitor your tweet timeline, looking for tweets matching the
following format:

   s/this/that/

When a matching tweet is found, the app should:

1. locate the previous tweet in your timeline,
#. verify that it matches the first part of the replacement expression,
#. perform the replacement and tweet it,
#. delete both the original tweet and the tweet specifying the replacement.
