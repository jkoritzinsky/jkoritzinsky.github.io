---
layout: post
title: SharpGenTools 1.1.0 Released!
categories: [SharpGenTools, release]
tags: [SharpGenTools, release]
---

SharpGenTools 1.1 is now on NuGet! We've fixed a variety of issues!

## Fixed Issues

- Issue #42: Support unsigned types for enums.
- Issue #53: `DuplicateKeyException` in `ShadowContainer`
- Issue #57: Make Shadow initialization threadsafe.
- Issue #61: Make methods of the form `RESULT SetX(T value)` generate a property of the form `T X {set;}` and support building get/set properties.
- Issue #62: Support spaces in project directory path.
- Issue #64: Missing null check in marshal-from-native code generation.
- Issue #66: Missing null check in native structure `stackalloc`.
- Issue #67: Fast Out Parameters cannot be callback interfaces.
- Issue #68: Arrays of structures with native views sometimes (it is struct-as-class) doesn't generate fixed statement.
- Issue #70: InOut Optional Pointer-to-struct parameters generate invalid codegen.
- Miscellaneous fixes in the marshalling code generator.

## New Features

**Shadow auto-generation**: There's a new mapping rule, `autogen-shadow`. When set to `true` and the interface being mapped is a callback interface, the code-generator will generate the interface methods and the shadow/vtbl classes! This simplifies the work you have to do to support managed callback implementations by doing basically all of the work for you! There is more in-depth information in the docs.

**Limitations**:

- If a method in the interface takes an array, then you'll have to manually write the shadow and vtbl classes. The generator currently does not have a way to understand the length of the array.
- If the callback is a dual callback (native implementation is generated) then you need to have a `keep-implement-public="true"` rule for all the methods of the interface.
- Property generation currently does not interface well with the shadow auto-generator, so you should add a `property="false"` rule for all the methods of the interface that might generate properties.