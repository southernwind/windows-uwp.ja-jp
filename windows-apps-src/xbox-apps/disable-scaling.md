---
title: スケーリングを無効にする方法
description: 既定の倍率を無効にする方法を説明します。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 6e68c1fc-a407-4c0b-b0f4-e445ccb72ff3
ms.localizationpriority: medium
ms.openlocfilehash: 44688ff40792ba2ee72cbd1d96bae1ac59834efa
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57604747"
---
# <a name="how-to-turn-off-scaling"></a>スケーリングを無効にする方法   
アプリケーションは既定で、XAML アプリの場合は 200% に、HTML アプリの場合は 150% に拡大されます。 また、既定の倍率を無効にすることもできます。 これにより、アプリケーションをデバイスの実際のピクセル サイズ (1910 x 1080 ピクセル) で使うことができるようになります。   
   
## <a name="html"></a>HTML   
次のコード スニペットを使って拡大縮小率を無効にすることができます。 
   
```
var result = Windows.UI.ViewManagement.ApplicationViewScaling.trySetDisableLayoutScaling(true);
```

また、Web 対応の次の方法を使うこともできます。   

```   
@media (max-height: 1080px) {   
    @-ms-viewport {   
        height: 1080px;   
    }   
}   
```

## <a name="xaml"></a>XAML
次のコード スニペットを使って拡大縮小率を無効にすることができます。   
   
```
bool result = Windows.UI.ViewManagement.ApplicationViewScaling.TrySetDisableLayoutScaling(true);
```
   
## <a name="directxc"></a>DirectX/C++   
DirectX/C++ アプリケーションはスケーリングされません。 自動スケーリングは、HTML アプリケーションと XAML アプリケーションにのみ適用されます。  

## <a name="see-also"></a>関連項目
- [Xbox のベスト プラクティス](tailoring-for-xbox.md)
- [Xbox One の UWP](index.md)
