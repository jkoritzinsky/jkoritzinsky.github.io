---
layout: post
title: SharpGenTools 1.0.0 RC1 Release
categories: [SharpGenTools, release]
tags: [SharpGenTools, release, rc]
---

SharpGenTools 1.0.0 RC1 is out! I've done a lot of work since the 1.0.0 Beta to clean up the code and better integrate the `SharpGenTools.Sdk` package into MSBuild. There are a number of breaking changes, and some features from the SharpDX version that have been removed since they have been obseleted by the MSBuild integration. Take a look!

# Added Features

* Patching Signed Assemblies
  * SharpPatch integration in the SDK can now patch signed assemblies as long as the `sn.exe` executable can be found via MSBuild or in the PATH.

* Separate MSBuild Tasks
   * Each step in the code-gen process is a separate task with matching targets to execute each step and correctly skip tasks when files have not been updated.

* SDK rules in mapping files
   * SDK rules encapsulate common headers and automatically locate them in the system. Currently, SDK rules can locate the C++ standard library by the name `StdLib` and the Windows SDK by the name `WindowsSdk` and the requested version.

# Changed Features

* Generate code with Roslyn
   * Code generation is now done directly with the SyntaxFactory APIs in Roslyn instead of via T4. This makes it easier to refactor/update the code generation in the future.

* Resolving consumed `BindMapping` files in packages has been changed to embed it into the package and create a `.props` file in the resulting package and make the resolution automatic. This may change again in the future, but this method is forward-compatible with any future changes.

# Removed Features

* SharpGen no longer requires the `VcToolsPath` variable or commandline parameter.
   * The SDK Rule feature allows SharpGen to automatically resolve the standard library on Windows systems with VS2017 installed, so we don't need to specify a path anymore.
* `AppType`s have been removed.
   * Since there would only ever be one `AppType` for a specific assembly built for a specific `TargetFramework` and the feature can be emulated via MSBuild scripting, I've removed the `AppType` feature.