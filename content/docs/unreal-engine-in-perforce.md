---
draft: true
title: Unreal Engine in Perforce
date: 2022-07-05T02:07:08-04:00
tags:
  - unreal
  - perforce
imageIcon: fa-sharp fa-light fa-code-pull-request-draft
summary: Thoughts and tips on keeping Unreal Engine tracked in perforce.
---
This document focuses on notes and tips for managing the Unreal Engine source within perforce. It refers to a workflow in which the Epic Games Unreal depot is essentially mirrored from their perforce to your studio perforce.

# The Pros

- Everything in one place.
  - You'll never go back to a project and be unsure which engine was used or have to sync or download it from somewhere else.
- A single timeline.
  - As a project goes on and engine versions are upgraded, everything is tracked in perforce together, so there's never an issue about which version of Unreal was used for which changelist of a project.
- Easier for future you.
  - As already implied in the previous points, everything together helps tremendously when you need to come back to a past project, and all you have to do to get up and running is sync.
- It's required for [setting up UGS](/notes/ugs-setup), which provides a number of workflow benefits.
- Less time troubleshooting.
  - Think there's a problem with the engine? Clean workspace in UGS or sync it fresh on a different machine, there's no uncertainty about the environment when both the engine and project are coming from the same place.
- It will actually take up _less_ space in the long run.
  - Perforce does not need to store duplicates of archive files on the server when you branch or copy the engine into different project depots, meaning you only end up storing one copy of the engine on your sever, plus the deltas for each engine version. Populating a new project depot takes seconds.
  - With the Engine in perforce, and after setting up UGS, you can also filter what content from the Engine you sync, meaning you skip some unnecessary content and keep a smaller footprint on each users machine.

# The Cons

- Depending on how you actually add Unreal to perforce and track it, it can be a lot of work! there's over 200k files!
- Some operations are really slow.
  - For instance trying to run `p4 reconcile` on the engine to find changes or clean the workspace can take too much time to be useful, but there are alternates and solutions like UGS's Clean Workspace feature.
- If you track the engine in perforce, but _don't_ setup UGS, distributing binaries would be very painful. Use UGS.
- See this article on how to more easily [integrate and manage Unreal in perforce](/notes/integrating-unreal-from-epic).
- Though the decision of whether to build Unreal from source at all is slightly separate, with Unreal in perforce you will be building from source and that means longer initial build times.
  - An average good build time for the engine from scratch is ~45min.
  - If you stay on top of which configurations you are building and why, this should only need to happen the first time, when updating the engine, or when modifying engine headers that are really popular.

# What are the alternatives?

- Use a stock engine version from the Epic Games Launcher
  - This works well for a lot of new teams, but you'll eventually hit a wall or limitation.
  - Stock engine versions (aka Installed Builds) can't build certain configurations (like Test).
  - If you want to ship to a console platform, you'll need to build the engine from source.
  - Submitting project and plugin binaries is not that bad, but UGS is still better.
- Track the engine in a separate but associated git repo.
  - It's possible to fork and track changes to Unreal from github, but maintaining a pristine environment and easily being able to associate project changelists with engine versions is very difficult.
- See the [deploying unreal](https://docs.unrealengine.com/5.0/en-US/deploying-unreal-engine/) docs for more info.
