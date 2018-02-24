---
layout: post
title: SharpGenTools 1.0.0 Released!
categories: [SharpGenTools, release]
tags: [SharpGenTools, release]
---

SharpGenTools 1.0.0 is out! I've done a lot of work since 1.0.0 RC1 to refactor a lot of the code and strip down the runtime support classes, while also fixing a lot of the bugs.

# Added Features

* External Documentation Files
  * Documentation for generated code can be included in external files.
* Refactored Documentation Providers
  * Refactored documentation providers to be asynchronous.
* Refactored out MSDN and COM support
  * The MSDN Doc Provider is now in a separate package.
  * The COM runtime support classes/mappings are in a separate SharpGen.Runtime.COM package.