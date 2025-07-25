---
layout: post
title: "The Hidden Cost of try-catch"
date: 2025-04-12 00:00:00 +0800
categories: [c++]
tags: [exceptions, performance]
---

While adding bounds‑checked accessors to a container class, I wondered whether
I could simply call `at()` and catch the exception when a key was missing.
After all, `std::out_of_range` seems like a perfect signal for an invalid
lookup.  However, benchmarking revealed a **significant performance penalty**
when using C++ exceptions for control flow【51398355243624†L35-L66】.

In this post I present a small experiment comparing two implementations: one
using `operator[]` with a manual bounds check and another using `at()` wrapped
in a `try-catch` block.  I built the container with `-O2` and measured
execution times using `perf` and `valgrind`.  The version with exceptions was
substantially slower due to the overhead of stack unwinding and exception
handling【51398355243624†L90-L112】.

The takeaway is that exceptions in C++ should be reserved for truly
exceptional situations.  Using them for routine control flow can degrade
performance and harm code readability.  In this case, implementing an
explicit check for the key’s existence before accessing the map resulted in
both faster execution and clearer semantics.
