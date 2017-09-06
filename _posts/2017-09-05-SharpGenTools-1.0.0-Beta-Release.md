---
layout: post
title: SharpGenTools 1.0.0-Beta Release
categories: [SharpGenTools, release]
tags: [SharpGenTools, release, beta]
---

Last night I released a beta version of SharpGenTools, a suite of tools and MSBuild integrations to enable easy C# code generation for interop with C++ and COM code while relying only on the C++ headers. Today, we'll go through some of the history of SharpGenTools, a quick overview of the architecture of them, and a quick look at some of the future roadmap. Over time I'll do more in-depth blog posts on the architecture of the tools since how some of how it all works is really interesting.

# History of SharpGenTools
Up until this release, SharpGenTools has been a subproject of SharpDX, a state-of-the-art library of C# interfaces for nearly all of the DirectX API surface. The code generator in SharpGenTools, SharpGen, has powered most of that, generating a large portion of SharpDX's code.

I spoke with the mantainer of SharpDX, Alexandre Mutel, while I was contributing to SharpDX about SharpGen as I was moving SharpDX development over to Visual Studio 2017 from VS2015 about the design of SharpGen and SharpCli (now SharpPatch). He talked about how SharpGen was kind of just patched together as needed to enable new mappings from DirectX and to move development to new VS versions.

I am always extremely interested in the engineering problems that arise from throwing different languages/runtimes/frameworks together while trying to create a new project, so I asked Alexandre if I could spin off SharpGen and SharpCli into their own project, and he gave me a green light. Back in May, I extracted the history for SharpGen and SharpCli into the SharpGenTools repo under my account and went to work on cleanup, testing, and modernization. A few months later, we're here with an independent SharpGenTools project!

# Architecture Overview
## SharpGen
SharpGen is the core piece of SharpGenTools. It is the driver for all of the code-gen. You give `.xml` files that contain rules on how to map the C++ code to C# code. It loads in the files, and generates an entry C++ header file. Then it uses CastXML, a C++ compiler that outputs XML, to generate a model of the C++ code, which it then parses into its own model. Then it uses the mapping rules to create the API surface of the C# code. Finally, it generates the C# code, inserting custom marshalling code so it can be ready to use the `calli` instruction to directly call into the C++/COM library.
## SharpPatch
C# can't use the `calli` instruction directly, so SharpPatch uses Mono.Cecil to patch the assembly and replace a set of known method calls with `calli` instructions. It also replaces some other methods that aren't possible to write in C#.
## SharpGenTools.Sdk
This project handles all of the MSBuild integration to make development with SharpGen and SharpPatch in consuming projects as close to regular C# development. This is the NuGet package most people would use to use SharpGenTools.

It requires use of new SDK-style projects and MSBuild 15.3 or newer since it uses a few features that are exlusive to the new SDK-style projects and depends on MSBuild 15.3 bugfixes.

You set up a mapping file by just using the `SharpGenMapping` item type, and it will generate the code and flow type name mappings down through consuming projects that use SharpGenTools so they don't have to worry about copying their dependencies mapping files. It works across project to project references, and direct PackageReference references. This makes development of SharpGenTools-using libraries dozens of times easier by automating some of the most tedious work it used to require if you tried to use it outside of SharpDX.
## SharpGen.Runtime
SharpGen.Runtime contains the core classes to enable development of libraries that use SharpGenTools, as well as some extremely common Win32 APIs. Users would also want to install this package unless they want to write their own core classes.

I will go into more depth on the different core classes in SharpGen.Runtime in the future. This blog post is already getting pretty long.

# Roadmap
1.0.0
- More code cleanup
- Many more tests to verify code-gen

1.0.0 or Post-1.0.0
- Separate SharpGen.Runtime.Win32 package for the core Win32 classes
- Re-integration into SharpDX

1.0.0 to 2.0.0
- Auto-generate "shadow" implementations and v-tables (the classes that let you pass C# objects as callback objects to C++/COM)
- Support for C++ classes with virtual and nonvirtual member functions
- Better support for non `__stdcall` functions and function callbacks

2.0.0
- Basic Linux/POSIX ABI mappings presupplied.
