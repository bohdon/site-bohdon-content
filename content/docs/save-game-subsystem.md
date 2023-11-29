---
draft: true
title: Save Game Subsystem
date: 2022-08-19T16:43:37.998Z
tags:
  - unreal
  - data
  - programming
summary: Overview of how to setup a save game subsystem for more easily managing
  save states and debug playing from any level.
---
### Goals and Features
- Playing from entry level / main menu does not perform any debug save game loading or setup
- Playing from any other level automatically loads a debug save character slot that auto saves on stop PIE

### Overview
There's a lot of aspects to it, but some high level points are:
- Save game subsystem to manage a shared USaveGame instance, that can create, load, save, and auto save with cooldowns easily. It also has a setting `bIsSavingDisabled` which means you can continuously load a debug save slot and not worry about modifying it.
- Game instance with a EMyGameInstanceState that looks like:

```c++
enum class EMyGameInstanceState : uint8
{
	// the game is waiting to start up
	WaitingToStart,
	// the game is starting up
	Starting,
	// in the main menu, waiting for save slot load
	MainMenu,
	// save slot is loaded and being played
	Playing,
	// a level is being played via PIE, without navigating through the proper startup
	DebugPlaying,
};
```

- `AMyGameModeBase` that has a setting for whether this game mode should try to debug play, and if so, in `InitGame`, checks if the game instance state is `WaitingToStart` (meaning you didn't start through the proper entry maps) and calls `MyGameInstance->StartDebugPlay()` which handles instant loading the save game and opening the debug character slot.
