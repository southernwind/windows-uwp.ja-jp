---
Description: ポップアップ アニメーションを使って、ポップアップ UI やカスタム ポップアップ UI 要素の表示と非表示を切り替えます。 ポップアップ要素とは、アプリのコンテンツの上に表示されるコンテナーのことで、ユーザーがポップアップ要素の外部をタップまたはクリックすると消えます。
title: ポップアップ UI のアニメーション
ms.assetid: 4E9025CE-FC90-4d4c-9DE6-EC6B6F2AD9DF
label: Motion--Pop-up animations
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 78adc32dccc66b98e413796d9cfe20b2a158261e
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970537"
---
# <a name="pop-up-ui-animations"></a>ポップアップ UI のアニメーション



ポップアップ アニメーションを使って、ポップアップ UI やカスタム ポップアップ UI 要素の表示と非表示を切り替えます。 ポップアップ要素とは、アプリのコンテンツの上に表示されるコンテナーのことで、ユーザーがポップアップ要素の外部をタップまたはクリックすると消えます。

> **重要な API**: [**PopInThemeAnimation クラス**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopInThemeAnimation)、[**PopupThemeTransition クラス**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopupThemeTransition)


## <a name="dos-and-donts"></a>推奨と非推奨


-   ポップアップ アニメーションは、アプリ ページ自体には含まれないカスタム ポップアップ UI 要素を表示したり非表示にしたりするために使います。 Windows で用意されているコモン コントロールには、既にこのアニメーションが組み込まれています。
-   ヒントやダイアログにポップアップ アニメーションを使わないでください。
-   アプリのメイン コンテンツ内の UI の表示と非表示を切り替えるためにポップアップ アニメーションを使わないでください。ポップアップ アニメーションは、メイン アプリ コンテンツの上に表示されるポップアップ コンテナーの表示と非表示を切り替えるときにのみ使います。

## <a name="related-articles"></a>関連記事

* [アニメーションの概要](https://docs.microsoft.com/windows/uwp/graphics/animations-overview)
* [ポップアップ UI のアニメーション化](https://docs.microsoft.com/previous-versions/windows/apps/jj649433(v=win.10))
* [クイック スタート: ライブラリのアニメーションを使った UI のアニメーション化](https://docs.microsoft.com/previous-versions/windows/apps/hh452703(v=win.10))
* [**PopInThemeAnimation クラス**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopInThemeAnimation)
* [**PopOutThemeAnimation クラス**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopOutThemeAnimation)
* [**PopupThemeTransition クラス**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopupThemeTransition)

 

 




