---
draft: true
title: Game Time User Widget
date: 2022-07-04T22:22:53-04:00
tags:
  - unreal
  - ui
image: /img/notes-game-time-user-widget.jpg
imageIcon: fa-sharp fa-light fa-stopwatch
summary: Create a UserWidget that ticks using Game time so that it respects pausing and slowmo in animations and timers.
---

User Widgets by default do not use game time when ticking, which means ui animations, delays, or timers in a widget aren't affected by game time dilation or pausing. You can override how a widget ticks, though, allowing you to make user widget animations that stay in sync with game time.

- Subclass and override `UUserWidget::NativeTick`.
- Calculate a new running delta time using `World->GetTimeSeconds()` which includes game time dilation and pausing.
- Call the super tick function using the calculated delta time.
- The `UUserWidget::NativeTick` super function handles ticking animations, the latent action manager (timers and delays), and blueprint Tick, which means all of those will correctly use the given delta time.
- The example below allows each widget to control whether game time or slate time is used, so that it can be the parent class of any user widget without enforcing its functionality.

`GameTimeUserWidget.h`

```c++
/**
 * A user widget that uses game time when ticking animations and latent actions
 */
UCLASS()
class MYGAME_API UGameTimeUserWidget : public UUserWidget
{
	GENERATED_BODY()

public:
	UGameTimeUserWidget(const FObjectInitializer& ObjectInitializer);

	/** When true, tick animations, actions, and the blueprint tick using game time, affected by pausing and time dilation */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, BlueprintSetter = SetUseGameTime)
	bool bUseGameTime;

	UFUNCTION(BlueprintSetter)
	void SetUseGameTime(bool bEnable);

protected:
	/** The last game time used to tick actions and animations */
	float LastWidgetTickTime;

	virtual void NativeTick(const FGeometry& MyGeometry, float InDeltaTime) override;
};
```

`GameTimeUserWidget.cpp`

```c++
UGameTimeUserWidget::UGameTimeUserWidget(const FObjectInitializer& ObjectInitializer)
	: Super(ObjectInitializer),
	  bUseGameTime(true)
{
}

void UGameTimeUserWidget::SetUseGameTime(bool bEnable)
{
	if (bUseGameTime != bEnable)
	{
		bUseGameTime = bEnable;
		LastWidgetTickTime = 0.f;
	}
}

void UGameTimeUserWidget::NativeTick(const FGeometry& MyGeometry, float InDeltaTime)
{
	UWorld* World = GetWorld();
	if (bUseGameTime && World)
	{
		// calculate game delta time
		const float GameTime = World->GetTimeSeconds();
		const float GameDeltaTime = LastWidgetTickTime == 0.f ? 0.f : (GameTime - LastWidgetTickTime);
		LastWidgetTickTime = GameTime;

		// use game delta time to tick animations and latent actions
		Super::NativeTick(MyGeometry, GameDeltaTime);
	}
	else
	{
		Super::NativeTick(MyGeometry, InDeltaTime);
	}
}
```
