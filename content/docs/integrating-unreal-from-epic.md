---
draft: true
title: "Integrating Unreal from Epic"
date: 2022-07-05T01:31:46-04:00
tags:
  - unreal
  - perforce
image:
imageIcon: fa-sharp fa-light fa-merge
summary: How to integrate and update Unreal Engine from Epic's perforce, so you can stay up to date while keeping the Engine in perforce.
---

It can be cumbersome to manage a custom build of Unreal Engine, but it's essential for many reasons, including console development. This guide covers how to add the engine to your studio's perforce and how to keep it up date.

> If you're uncertain about keeping Unreal in perforce, see this page on [why you should keep Unreal in perforce](/notes/unreal-engine-in-perforce)

### Overview

- A pristine copy of Unreal Engine will exist in your studio's perforce, e.g. at `//UE5`
- Only release versions need to be integrated, meaning any major, minor, or patch versions that get officially released.

#### Depots
```bash
//UE5/Main # a pristine copy of Unreal Engine unmodified from Epic, updated with each release version
//MyProject/Main # the project depot
```

TODO

### Additional Thoughts

- This setup assumes that your studio will work on a project basis (e.g. 1 depot per project), and any engine modifications that may occur are specific to the project.
- If you intend to manage a customized engine that will be used across multiple project depots, you might want to setup an additional engine development depot and integrate into projects from there instead of `//UE5`.
