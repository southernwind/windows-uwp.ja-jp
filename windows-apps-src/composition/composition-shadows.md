---
author: daneuber
title: コンポジションのシャドウ
description: シャドウ Api では、動的なカスタマイズ可能なシャドウを UI コンテンツを追加することができます。
ms.author: jimwalk
ms.date: 07/16/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: 84e12d6c3e25a18902aaa55011949dd5b5ff97ca
ms.sourcegitcommit: 53ba430930ecec8ea10c95b390fe6e654fe363e1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "3413841"
---
# <a name="shadows-in-windows-ui"></a>Windows UI でのシャドウ

[DropShadow](/uwp/api/Windows.UI.Composition.DropShadow)クラスは、 [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual)または[LayerVisual](/uwp/api/windows.ui.composition.layervisual) (視覚効果のサブツリー) に適用可能な構成可能なシャドウを作成する手段を提供します。 ビジュアル レイヤー内のオブジェクトの慣例がでは、Compositionanimation を使用して、DropShadow のすべてのプロパティをアニメーション化できます。

## <a name="basic-drop-shadow"></a>基本的なドロップ シャドウ

基本的なシャドウを作成するには、新しい DropShadow を作成し、visual に関連付けることだけです。 影が既定では四角形です。 標準的な一連のプロパティは、シャドウの見た目や操作感を調整できます。

```cs
var basicRectVisual = _compositor.CreateSpriteVisual();
basicRectVisual.Brush = _compositor.CreateColorBrush(Colors.Blue);
basicRectVisual.Offset = new Vector3(100, 100, 20);
basicRectVisual.Size = new Vector2(300, 300);

var basicShadow = _compositor.CreateDropShadow();
basicShadow.BlurRadius = 25f;
basicShadow.Offset = new Vector3(20, 20, 20);

basicRectVisual.Shadow = basicShadow;
```

![四角形のビジュアルで基本的な DropShadow](images/rectangular-dropshadow.png)

## <a name="shaping-the-shadow"></a>シャドウを形成

DropShadow の形状を定義するいくつかの方法はあります。

- DropShadow 図形を既定では **、既定の使用**- は CompositionDropShadowSourcePolicy の '既定' モードによって定義されます。 SpriteVisual、既定値は四角形、マスクが提供されている場合を除き、します。 LayerVisual、既定値は、ビジュアル オブジェクトのブラシのアルファ マスクを継承します。
- **マスクを設定**、シャドウの不透明マスクを定義する[マスク](/uwp/api/windows.ui.composition.dropshadow.mask)プロパティを設定することがあります。
- **継承マスクを使うことを指定する**– では、 [CompositionDropShadowSourcePolicy](/uwp/api/windows.ui.composition.compositiondropshadowsourcepolicy)を使用する[SourcePolicy](/uwp/api/windows.ui.composition.dropshadow.sourcepolicy)プロパティを設定します。 ビジュアル オブジェクトのブラシのアルファから生成されたマスクを使用する InheritFromVisualContent します。

## <a name="masking-to-match-your-content"></a>コンテンツに合わせてマスク

シャドウ マスク プロパティに、ビジュアル オブジェクトのブラシを使用するか、設定、コンテンツのマスクを自動的に継承をシャドウのビジュアル オブジェクトのコンテンツに合わせて、シャドウを実行する場合にします。 LayerVisual を使っている場合、シャドウは既定では、マスクを継承します。

```cs
var imageSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/myImage.png"));
var imageBrush = _compositor.CreateSurfaceBrush(imageSurface);

var imageSpriteVisual = _compositor.CreateSpriteVisual();
imageSpriteVisual.Size = new Vector2(400,400);
imageSpriteVisual.Offset = new Vector3(100, 500, 20);
imageSpriteVisual.Brush = imageBrush;

var shadow = _compositor.CreateDropShadow();
shadow.Mask = imageBrush;
// or use shadow.SourcePolicy = CompositionDropShadowSourcePolicy.InheritFromVisualContent;
shadow.BlurRadius = 25f;
shadow.Offset = new Vector3(20, 20, 20);

imageSpriteVisual.Shadow = shadow;
```

![マスク ドロップ シャドウで接続されている web 画像](images/ms-brand-web-dropshadow.png)

## <a name="using-an-alternative-mask"></a>代替のマスクを使用します。

場合によっては、図形、シャドウのビジュアル オブジェクトのコンテンツをそれと一致しないようにすることがあります。 この効果を実現するには、アルファを含むブラシを使用して、マスク プロパティを明示的に設定する必要があります。

次の例では、2 つのサーフェスに視覚的なコンテンツ用とシャドウ マスク用に 1 つを読み込みます。

```cs
var imageSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/myImage.png"));
var imageBrush = _compositor.CreateSurfaceBrush(imageSurface);

var circleSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/myCircleImage.png"));
var customMask = _compositor.CreateSurfaceBrush(circleSurface);

var imageSpriteVisual = _compositor.CreateSpriteVisual();
imageSpriteVisual.Size = new Vector2(400,400);
imageSpriteVisual.Offset = new Vector3(100, 500, 20);
imageSpriteVisual.Brush = imageBrush;

var shadow = _compositor.CreateDropShadow();
shadow.Mask = customMask;
shadow.BlurRadius = 25f;
shadow.Offset = new Vector3(20, 20, 20);

imageSpriteVisual.Shadow = shadow;
```

![円で接続されている web 画像マスク ドロップ シャドウ](images/ms-brand-web-masked-dropshadow.png)

## <a name="animating"></a>アニメーション化

標準のビジュアル レイヤーでは、コンポジションのアニメーションを使用して、DropShadow プロパティをアニメーション化できます。 以下は、上記の影のぼかし半径をアニメーション化する散点サンプルからのコードを変更します。

```cs
ScalarKeyFrameAnimation blurAnimation = _compositor.CreateScalarKeyFrameAnimation();
blurAnimation.InsertKeyFrame(0.0f, 25.0f);
blurAnimation.InsertKeyFrame(0.7f, 50.0f);
blurAnimation.InsertKeyFrame(1.0f, 25.0f);
blurAnimation.Duration = TimeSpan.FromSeconds(4);
blurAnimation.IterationBehavior = AnimationIterationBehavior.Forever;
shadow.StartAnimation("BlurRadius", blurAnimation);
```

## <a name="shadows-in-xaml"></a>XAML でのシャドウ

複雑なフレームワークの要素にシャドウを追加する場合は、XAML とコンポジションの間でのシャドウとの相互運用機能をいくつかの方法があります。

1. Windows コミュニティ ツールキットで利用可能な[DropShadowPanel](https://github.com/Microsoft/UWPCommunityToolkit/blob/master/Microsoft.Toolkit.Uwp.UI.Controls/DropShadowPanel/DropShadowPanel.Properties.cs)を使用します。 その使用方法の詳細については、 [DropShadowPanel ドキュメント](https://docs.microsoft.com/windows/uwpcommunitytoolkit/controls/DropShadowPanel)を参照してください。
1. シャドウのホストとして使用すると、XAML ハンドアウト Visual を関連付けることにビジュアルを作成します。
1. コンポジションのサンプル ギャラリーの[SamplesCommon](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SamplesCommon/SamplesCommon)カスタム CompositionShadow コントロールを使います。 この例での使用を参照してください。

## <a name="performance"></a>パフォーマンス

ビジュアル レイヤーは、多くの最適化を効率的で使用可能な効果を作成するには、シャドウを生成できる、比較的安価操作によっては、どのようなオプションを設定します。 高レベル '' のコストさまざまな種類のシャドウを以下に示します。 特定シャドウのコストがかかる場合がありますがまだあります特定のシナリオで慎重に使用する適切な注意してください。

シャドウ特性| 費用
------------- | -------------
[四角形の領域切り取り]    | 低
Shadow.Mask      | 高
CompositionDropShadowSourcePolicy.InheritFromVisualContent | 高
静的なぼかし Radius | 低
Radius のぼかしをアニメーション化 | 高

## <a name="additional-resources"></a>その他のリソース

- [コンポジション DropShadow API](/uwp/api/Windows.UI.Composition.DropShadow)
- [WindowsUIDevLabs GitHub リポジトリ](https://github.com/Microsoft/WindowsUIDevLabs)