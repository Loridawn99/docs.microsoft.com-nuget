---
title: Pre-release versions in NuGet packages | Microsoft Docs
author: kraigb
ms.author: kraigb
manager: ghogen
ms.date: 8/14/2017
ms.topic: article
ms.prod: nuget
ms.technology: null
ms.assetid: df6a366a-22c1-47bb-8017-18231311ce88
description: Guidance for building pre-release packages
keywords: versioning, NuGet package versioning, NuGet prerelease versions, NuGet prerelease packages, preview package versions, RC package versions, Beta package versions, NuGet semantic versioning
ms.reviewer:
- karann-msft
- unniravindranathan
---

# Building pre-release packages

Whenever you release an updated package with a new version number, NuGet considers that one as the "latest stable release" as shown, for example in the Package Manager UI within Visual Studio:

![Package Manager UI showing the latest stable release](media/Prerelease_01-LatestStable.png)

A stable release is one that's considered reliable enough to be used in production. The latest stable release is also the one that will be installed as a package update or during package restore (subject to constraints as described in [Reinstalling and updating packages](../consume-packages/reinstalling-and-updating-packages.md)).

To support the software release lifecycle, NuGet 1.6 and later allows for the distribution of pre-release packages, where the version number includes a semantic versioning suffix such as `-alpha`, `-beta`, or `-rc`. For more information, see [Package versioning](../reference/package-versioning.md#pre-release-versions).

You can specify such versions in two ways:

- `.nuspec` file: include the semantic version suffix in the `version` element:

    ```xml
    <version>1.0.1-alpha</version>
    ```

- Assembly attributes: when building a package from a Visual Studio project (`.csproj` or `.vbproj`), use the `AssemblyInformationalVersionAttribute` to specify the version:

    ```cs
    [assembly: AssemblyInformationalVersion("1.0.1-beta")]
    ```

    NuGet picks up this value instead of the one specified in the `AssemblyVersion` attribute, which does not support semantic versioning.

When you’re ready to release a stable version, just remove the suffix and the package takes precedence over any pre-release versions. Again, see [Package versioning](../reference/package-versioning.md#pre-release-versions).


## Installing and updating pre-release packages

By default, NuGet does not include pre-release versions when working with packages, but you can change this behavior as follows:

- **Package Manager UI in Visual Studio**: In the **Manage NuGet Packages** UI, check the **Include prerelease** box:

    ![The Include prerelease checkbox in Visual Studio](media/Prerelease_02-CheckPrerelease.png)

    Setting or clearing this box will refresh the Package Manager UI and the list of available versions you can install.

- **Package Manager Console**: Use the `-IncludePrerelease` switch with the `Find-Package`, `Get-Package`, `Install-Package`, `Sync-Package`, and `Update-Package` commands. Refer to the [PowerShell Reference](../tools/powershell-reference.md).

- **NuGet CLI**: Use the `-prerelease` switch with the `install`, `update`, `delete`, and `mirror` commands. Refer to the [NuGet CLI reference](../tools/nuget-exe-cli-reference.md)


## Semantic versioning

The [Semantic Versioning or SemVer convention](http://semver.org/spec/v1.0.0.html) describes how to utilize strings in version numbers to convey they meaning of the underlying code.

In this convention, each version has three parts, `Major.Minor.Patch`, with the following meaning:

- `Major`: Breaking changes
- `Minor`: New features, but backwards compatible
- `Patch`: Backwards compatible bug fixes only

Pre-release versions are then denoted by appending a hyphen and a string after the patch number. Technically speaking, you can use *any *string after the hyphen and NuGet will treat the package as pre-release. NuGet then displays the full version number in the applicable UI, leaving consumers to interpret the meaning for themselves.

With this in mind, it's generally good to follow recognized naming conventions such as the following:

- `-alpha`: Alpha release, typically used for work-in-progress and experimentation
- `-beta`: Beta release, typically one that is feature complete for the next planned release, but may contain known bugs.
- `-rc`: Release candidate, typically a release that's potentially final (stable) unless significant bugs emerge.

> [!Note]
> NuGet does not support [SemVer-compatible (v2.0.0)](http://semver.org/spec/v2.0.0.html) prerelease numbers with dot notation, as in `1.0.1-build.23`. You can use a form like `1.0.1-build23` but this is always considered a pre-release version.

Whatever suffixes you use, however, NuGet will give them precedence in reverse alphabetical order:

```
1.0.1
1.0.1-zzz
1.0.1-rc
1.0.1-open
1.0.1-beta12
1.0.1-beta05
1.0.1-beta
1.0.1-alpha2
1.0.1-alpha
```

As shown, the version without any suffix will always take precedence over pre-release versions. Note also that if you use numerical suffixes with pre-release tags that might use double-digit numbers (or more), use leading zeroes as in beta01 and beta05 to ensure that they sort correctly when the numbers get larger.
