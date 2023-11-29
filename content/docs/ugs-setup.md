---
draft: true
title: Simplified UGS Setup
date: 2022-07-05T01:31:46-04:00
tags:
  - unreal
  - ugs
  - perforce
imageIcon: fa-sharp fa-light fa-folder-tree
color: "#440022"
summary: How to setup Unreal Game Sync and distribute Unreal to your team.
---
I've noticed a number of people that have heard about UGS but think "large teams use UGS, but what can I use on a small team to easily distribute Unreal?" The answer is UGS! even for a team of two it can be extremely helpful and is worth setting up. This guide is intended to lower the barrier on setting up UGS and show how straightforward it is to use, even for small teams.

### What is Unreal Game Sync (UGS)?

- A simple application that facilitates syncing project and engine content from perforce, with the power of knowing how Unreal is setup, allowing it to perform other helpful operations.
- Made by Epic Games and used on Fortnite and other projects.
- Handles distributing precompiled binaries to artists, meaning they don't compile code, and devs never manually submit binaries.
- Allows  filtering out unneeded content, like PlayStation or Xbox content for devs that aren't building to those platforms, which reduces sync and compile times.
- A number of [other features](https://docs.unrealengine.com/5.0/en-US/unreal-game-sync-ugs-for-unreal-engine/).
- For more info on why Epic made UGS, see this [Fortnite workflow video](https://www.youtube.com/watch?v=p4RcDpGQ_tI).

### Why should I _not_ use UGS?

- You are using Blueprints only, no C++.
- You _really_ want to avoid using a custom build of the engine.
  - Note that you don't ever need to modify the engine, it can stay stock.
  - Also, once setup, you won't incur huge compile times while iterating on your project.
  - The main cost of a custom engine is disk space and occasional updates (more to come on that later).

---

## Studio Setup

This setup only needs to be done once per studio. Since the UGS application itself is developed out of band with any Unreal version and you rarely need to update it. You don't distribute UGS uniquely with each engine or project, just once per studio. The high level overview involves:

- Create a studio-specific version of UGS to simplify installation.
- Add UGS to Perforce, so it can be updated easily and users will automatically get new versions of UGS when available.
- Distribute the UGS installer.

### Create a studio-specific UGS application and installer

UGS _can_ be distributed right out of the box, but to make it easier on users, you should customize one setting which defines where the UGS application itself lives in your studio's Perforce. That way users can install and run it without any additional info.

- Get the latest version of UnrealEngine from [github](https://github.com/EpicGames/UnrealEngine) or perforce.
- Install [Wix](https://wixtoolset.org/releases/), which is a dependency for building the UGS application.
- In the engine, browse to [Engine/Source/Programs/UnrealGameSync](https://github.com/EpicGames/UnrealEngine/tree/release/Engine/Source/Programs/UnrealGameSync) and open `UnrealGameSync.sln` in Visual Studio.
- Edit `UnrealGameSync/DeploymentSettings.cs` to set the default depot path where the UGS application will live in perforce.
  - This path can be whatever you want, the main point is that it should be unrelated to any project or engine version. I recommend using `//UnrealGameSync/Main`, meaning UGS will live in it's own stream depot, in the Main branch.

```c#
public static readonly string DefaultDepotPath = "//UnrealGameSync/Main";
```

- Build the entire solution, this will compile the UGS installer and application.

```
/bin/UnrealGameSync.msi
TODO: list all files
```


### Add UGS to Perforce

- Create the `//UnrealGameSync` stream depot we used above for the default depot path.
- Create a mainline stream named `Main`.
- Create a workspace for the new stream.
- Add the UGS binaries at the path `//UnrealGameSync/Main/Release/...`

```
//UnrealGameSync/Main/Release/UnrealGameSync.exe
TODO: list all files being submitted
```

### Distribute the Installer

- TODO

---

## Per-Project Setup

This setup is done once per project. The high level overview includes:
- Add Unreal Engine to perforce
- 