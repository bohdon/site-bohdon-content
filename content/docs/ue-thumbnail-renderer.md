---
draft: false
title: Custom Thumbnail Renderer
date: 2022-06-02T00:51:53-04:00
tags:
  - unreal
  - c++
  - ui
  - editor
image: /img/notes-ue-thumbnail-renderer.jpg
images: [/img/notes-ue-thumbnail-renderer.jpg]
summary: Create a custom thumbnail renderer for your own classes, like an icon
  for a gameplay item data asset.
---

A custom thumbnail renderer allows you to draw anything you want in the thumbnail of an asset in the Content Browser. This is what allows meshes, materials, and textures in the editor to display something useful, instead of a generic icon. In this example a custom thumbnail renderer is used to give a unique icon to gameplay item data assets, so that you can browse for pickups, potions, or any other game items easily.

- Thumbnail renderers handle drawing asset thumbnails in the Content Browser.
- `UDefaultSizedThumbnailRenderer` is a commonly used renderer that defines a fixed size.
- In an Editor module, make sure at least these dependencies are added:

```c#
PublicDependencyModuleNames.AddRange(new string[]
{
	"Core",
	"UnrealEd",
});

PrivateDependencyModuleNames.AddRange(new string[]
{
	"CoreUObject",
	"Engine",
	"RenderCore",
	"Slate",
	"SlateCore",
	"UMG",
});
```

- Create a subclass, and implement `Draw`, then make calls to `Canvas->Draw*` using any available texture assets.

```c++
UCLASS()
class UGameplayItemDefThumbnailRenderer : public UDefaultSizedThumbnailRenderer
{
	GENERATED_BODY()

	virtual void Draw(UObject* Object, int32 X, int32 Y, uint32 Width, uint32 Height, FRenderTarget* Viewport, FCanvas* Canvas,
					  bool bAdditionalViewFamily) override;
};
```

- This example draws a thumbnail for a `UDataAsset` called `UGameplayItemDef`.
- Each item definition asset has a list of item fragments (just like items in [Lyra](https://docs.unrealengine.com/5.0/en-US/lyra-sample-game-in-unreal-engine/)).
- The thumbnail renderer retrieves a UI data fragment, and uses its `Icon` property, a `USlateBrushAsset`.

```c++
void UGameplayItemDefThumbnailRenderer::Draw(UObject* Object, int32 X, int32 Y, uint32 Width, uint32 Height, FRenderTarget* Viewport,
											 FCanvas* Canvas, bool bAdditionalViewFamily)
{
	const UGameplayItemDef* ItemDef = Cast<UGameplayItemDef>(Object);

	// retrieve the item fragment that contains ui data
	const UGameplayItemFragment_UIData* UIDataFrag = ItemDef->FindFragment<UGameplayItemFragment_UIData>();
	if (UIDataFrag)
	{
		// make sure the ui data has an icon
		const USlateBrushAsset* Icon = UIDataFrag->Icon;
		if (IsValid(Icon) && Icon->Brush.HasUObject())
		{
			const UTexture* TextureAsset = Cast<UTexture>(Icon->Brush.GetResourceObject());
			if (TextureAsset)
			{
				// determine the size of the texture
				const FSlateResourceHandle Handle = Icon->Brush.GetRenderingResource();
				const FSlateShaderResourceProxy* ResourceProxy = Handle.GetResourceProxy();
				const float U = ResourceProxy->StartUV.X;
				const float V = ResourceProxy->StartUV.Y;
				const float SizeU = ResourceProxy->SizeUV.X;
				const float SizeV = ResourceProxy->SizeUV.Y;

				// a custom background color to unify the assets appearance
				constexpr FLinearColor BGColor = FLinearColor(0.035f, 0.033f, 0.02f);
				const FLinearColor Color = Icon->Brush.TintColor.GetSpecifiedColor();

				// get the texture from the asset
				const FTexture* Texture = TextureAsset->GetResource();

				// draw the solid color background
				Canvas->DrawTile(X, Y, Width, Height, 0.f, 0.f, 0.f, 0.f, BGColor, GWhiteTexture, true);
				// draw the icon
				Canvas->DrawTile(X, Y, Width, Height, U, V, SizeU, SizeV, Color, Texture, true);
			}
		}
	}

	// TODO: add fallback icon
}
```

- Register the thumbnail renderer for a specific class in your editor module.
- The engine must be initialized, so you should use `FCoreDelegates::OnPostEngineInit` and perform the registration in a new `OnPostEngineInit` function to your module if necessary.

```c++
void FGameplayItemsEditorModule::StartupModule()
{
	FCoreDelegates::OnPostEngineInit.AddRaw(this, &FGameplayItemsEditorModule::OnPostEngineInit);
}

void FGameplayItemsEditorModule::OnPostEngineInit()
{
	if (GIsEditor)
	{
		UThumbnailManager::Get().RegisterCustomRenderer(
			UGameplayItemDef::StaticClass(),
			UGameplayItemDefThumbnailRenderer::StaticClass());
	}
}
```

- The thumbnail for an asset will be rendered the next time it's loaded.
- Thumbnails are cached and smaller compressed versions are saved when an asset is saved.
- Re-save existing assets to update their cached thumbnails.
