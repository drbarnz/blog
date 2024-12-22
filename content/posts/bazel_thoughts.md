+++
title = "Bazel Thoughts"
description = "A couple of musings about Bazel, the build system of monorepos"
date = 2024-12-22
updated = 2024-12-22

[taxonomies]
categories = ["tech"]
tags = ["bazel", "build", "thoughts"]
+++

## Bazel and Friends

Also known as Blaze internally at Google, [buck2](https://github.com/facebook/buck2) if you hail from Meta or
just [bazel](https://bazel.build/) to us regular folks. Either way it is a tool used to describe how to build
things through a side-car file that supports (usually through extensions / rules sets) many different toolchains
from Go, Python, Protobuf, and Rust.

> Wait, is that just `make`?

Well, yes and no. 

It is like `make` and a `Makefile` to describe inputs and actions on them but the big difference here is that it does
not operate on them with shell scripts, and (generally) does not require toolchains to be setup beforehand.
The toolchain specific rule sets includes code that will handle the installing of specific version of the toolchain,
configuring it with your targets, building, testing, test coverage reports and running them.

This can of course be done by composing a bunch of tools together, but having it all in-one and a fellow developer only needing
to install `bazel` (or to get meta [`bazelisk`](https://github.com/bazelbuild/bazelisk))

However, just like with `make`, it can build lazily and detect what has changed to spend as little time and energy as possible.
There is one cool part that takes this to the Google/Meta extreme I'll get into later.

## Syntax

The syntax of the side-car files, called `BUILD.bazel`, follows a subset of Python called [Starlark](https://github.com/bazelbuild/starlark) and
describes everything within that directory (referred to as a package -- just like in Go!)

```python
# All rules are explicitly imported using a `load()` call.
load("@rules_python//python:defs.bzl", "py_library", "py_test")

# An instance of a rule is made as such
# The `name` is unique within this directory/package.
py_library(
    name = "lib",
    srcs = ["lib.py"],
)

# Defining tests are done by rules that end with `_test`
py_test(
    name = "lib_test",
    srcs = ["lib_test.py"],
    # The `:lib` refers to the previous rule to use it here.
    # This forms a dependency tree between this library & tests!
    deps = [":lib"],
)
```

## Where does Bazel shine?

Bazel shines when you want to build a _lot_ of things consistently & reproducibly.

It does this through its [Content Addressable Storage (CAS)](https://bazel.build/remote/caching) and its support for [Remote Execution](https://bazel.build/remote/rbe).
These two things together are the magic that make building millions of lines of code possible from a "laptop^" (^: some magic applied).

The CAS can be used to check if your local inputs have every been used before to compile something, so you can just skip building it
as you can just down the result. This is super powerful as you can end up just building nothing as CI has already done it in a PR-check
so your local run of a program is just downing a binary that is exactly byte-for-byte what you would of built locally.

The Remote Exection is like `make -j 100` but across fleets of VMs. I've been using https://www.buildbuddy.io/ for a while now
and have saved hours a day of build time by spreading my builds across their CAS & Remote Execution environment.

## Cool/silly things with Bazel

There are some really cool and silly things you can do with Bazel, like in my homelab repository I use to manage
kubernetes manifests (builds way too many YAML files) I install any new custom resources (CRDs) and they're transformed
into schema and then used to check outputs using `kubeconform` without and additional plumbing.


## Annoying things with Bazel

When it goes wrong you end up needing to be the master of all build toolchains and Bazel.
It is often easier just going back to the regular toolchains for smaller projects.

## Summary

It is learning curve but an interesting adventure if you like to have reproducible builds!
