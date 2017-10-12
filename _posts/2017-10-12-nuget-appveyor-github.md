---
layout: post
title: Continuous Delivery For A Nuget Package Using Appveyor
date: "2017-10-12"
---

This post outlines some of the details involved in setting up a continuous delivery pipeline for a nuget package. 
The primary goal was to be able to deploy a new version of a package with a command from a development machine.
It is based on experiences in setting up [Hdq.Lib](https://github.com/hombredequeso/Hdq.Lib).
The primary tools used are [Appveyor]() for building and testing and [Nuget.org](https://www.nuget.org/) as the package repository.

# Pipeline
The besic pipeline from source code to nuget package is:

1. Solution containing C# project and corresponding test project
2. Local git repository
3. Github repository
4. Appveyor (build, run tests, create nuget package, deploy nuget package)
5. nuget.org (package repository)

<br>

# Requirements

The basic requirements were:

* Release of a new version of the library to nuget.org is by an operation from the developer's machine.
<br>The mechanism to implement this is is tagging a local git repository commit and pushing to GitHub.

```shell
git tag -a 1.2.3 -m "New release of amazing things"
git push --follow-tags
```

* Deployment builds (where a release is deployed to nuget.org) use version numbers for the package including the tag and the build number.
<br>For instance, if the tag is "1.2.3" and the appveyor build number (a continuously incrementing number) is 999, then the version number of the package would be "1.2.3.999"

* Non deployed builds including build/test/packaging should occur for commits that are not tagged. These builds should be versioned in a way that is clearly distinguished from the release builds.
<br>For example, when code is pushed to GitHub (origin), a build should occur in appveyor. If the appveyor build number was 888, and the git commit hash was ab1cd23, the build version number would be 0.0.0.888-ab1cd23

# Constraints

The following are constraints or behaviours of the systems being used:

* Nuget package numbers [must be of the form](https://docs.microsoft.com/en-us/nuget/reference/package-versioning): n.n.n.n[-ssss]
* Appveyor allows for any sort of build version number, but if you want to patch dll's with the version, it must be of the form n.n.n.[-ssss].
<br>However, while the build will be named after the full version number, the dll's will be patches with n.n.n.n, and the package pushed to nuget.org will also have the form n.n.n.n (i.e. the string from '-' onwards is dropped).

<br>

# Setup

Most of the setup from Visual Studio solution through to Github repository is not difficult, simply being a standard GitHub repository setup. Appveyor and Nuget are where most of the interesting work lies.

## Nuget

There are plenty of helpful guides to getting started with nuget. The two I found most helpful were:

* [Create and publish a package](https://docs.microsoft.com/en-us/nuget/quickstart/create-and-publish-a-package)
<br>A very simple getting started guide.
* [Creating NuGet packages](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package)
<br>Somewhat more detailed description of getting started.

A high level summary of the process (for more detail see [Create and publish a package](https://docs.microsoft.com/en-us/nuget/quickstart/create-and-publish-a-package)) is:

* download the nuget executable and put it in a location in the PATH.
* go to the directory containing the csproj file and create a spec file there

```shell
nuget spec
```

* Edit the newly created spec file. Mine ended up looking like [Hdq.Lib.nuspec](https://github.com/hombredequeso/Hdq.Lib/blob/ec6de422065da2548518f45868f258f9a8a76aa4/Hdq.Lib/Hdq.Lib.nuspec)
The most notable things in the nuspec file are:
  * \<id\> is going to be the name of the project in nuget.org.
  * \<version\> must be '$version' so that it be externally set (ultimately by appveyor) when the package is built.
* Create the nuget file. Ensure that the project is built first, or it won't work.

```shell
nuget pack projectname.csproj
```

This can be tested from the dev machine. If you do happen to try this take note that once a version number for a project is used, while it is possible to delete that version of the package from nuget.org, it is not possible to ever use that version number again. For testing purposes, the version number can be temporarily changed from '$version' to something like '0.0.0.1'.

* Upload the file package to nuget.org.

```shell
nuget push packageName.versionNumber.nupkg api-key-here -Source https://www.nuget.org/api/v2/package
```

## Appveyor

Creating an Appveyor account and connecting it to your GitHub repository is pretty much as simple as following their [Getting Started](https://www.appveyor.com/docs/). If you get this far, commits to the GitHub master branch will cause Appveyor to build the project and run the tests. Most of the subsequent setup I performed using the project "SETTINGS" user interface online. The settings allows for an appveyor.yml file to be exported. This file can then be add to the project and becomes the project definition, replacing whatever is defined by the settings UI. 

The main alterations to the default settings involve getting the versioning to work and deployment to nuget. Following is my [appveyor.yml](https://github.com/hombredequeso/Hdq.Lib/blob/ec6de422065da2548518f45868f258f9a8a76aa4/appveyor.yml), with comments liberally added to explain what is happening.

```yaml
# By default the version is set to the same thing as non-deployed builds.
# However, this is always overwritten using Update-AppveyorBuild below.
version: 0.0.0.{build}
image: Visual Studio 2017
# Package should use the Release, not Debug configuration.
configuration: Release
# Following is a powershell script that sets the version.
# If the build is triggered by a repo tag, then the version number 
# gets set to something appropriate for a deployed build (e.g. 1.2.3.456)
# Otherwise, the verison number gets set to something like 0.0.0.456-er43f3
init:
- ps: >-
    if ($env:APPVEYOR_REPO_TAG -eq "true")

    {
      Update-AppveyorBuild -Version "$($env:APPVEYOR_REPO_TAG_NAME).$($env:APPVEYOR_BUILD_NUMBER)"
    }

    else

    {
      Update-AppveyorBuild -Version "0.0.0.$($env:APPVEYOR_BUILD_NUMBER)-$($env:APPVEYOR_REPO_COMMIT.substring(0,7))"
    }
assembly_info:
# Patch true ensures that the assemblies version number 
# gets set to the build version number
  patch: true
  file: '**\AssemblyInfo.*'
  assembly_version: '{version}'
  assembly_file_version: '{version}'
  assembly_informational_version: '{version}'
# The nuget package always get published to the project feed. 
# This allows it to be downloaded and tested.
nuget:
  project_feed: true
before_build:
# Before building, restore any nuget libraries required.
- cmd: nuget restore Hdq.Lib.sln
# Build specifies that the nuget package must be published.
build:
  publish_nuget: true
  verbosity: normal
# The following is the details allowing for appveyor to push the 
# new version of the package to nuget.org.
deploy:
- provider: NuGet
  api_key:
    secure: j5g0Htw7zXZ/iAMHi68yb+9AZIO2bno642h/Djfe62Ux4ZiHIVNg0SpiIgI/BtqG
  artifact: /.*\.nupkg/
# Only deploy to nuget.org when the build was triggered by a tag.
  on:
    APPVEYOR_REPO_TAG: true
```


