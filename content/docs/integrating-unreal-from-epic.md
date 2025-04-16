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

# Overview

- A pristine copy of Unreal Engine will exist in your studio's perforce, e.g. at `//UE5`
- Only release versions need to be integrated, meaning any major, minor, or patch versions that get officially released.

# Depots
```bash
//UE5/Main # a pristine copy of Unreal Engine unmodified from Epic, updated with each release version
//MyProject/Main # the project depot
```

- This setup assumes that your studio will work on a project basis (e.g. 1 depot per project), and any engine modifications that may occur are specific to the project.
- If you intend to manage a customized engine that will be used across multiple project depots, you might want to setup an additional engine development depot and integrate into projects from there instead of `//UE5`.
- Small patch and backfix CLs should be integrated to the studio or project engine, to again keep the pristine depot intact.

# p4harmonize

Using a tool called p4harmonize, you can automate the sync, copying, and submitting
of Unreal from Epic's perforce into your pristine depot. The tool handles several helpful steps:

- Syncing from Epics p4
- Optimized reconciling of differences, including p4 file types
- Creating a workspace and staging the modified files for submission to your local depot

After its run, the changes can be reviewed and then submitted manually in P4V, etc.

## Config

- A config file defines the source and destination depots for p4harmonize.
- Read the p4harmonize docs for details, but heres an example config for completeness.

```

```

TODO: running first time vs subsequent?, submitting

# Project Integration

- After syncing a new version of UE, integrating from the pristine depot into your project is done with standard p4 integrate workflows.
- `p4 populate` can be used to branch the engine into a new empty project depot, and saves time since it runs only on the server.


