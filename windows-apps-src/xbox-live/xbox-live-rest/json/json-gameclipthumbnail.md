---
title: GameClipThumbnail (JSON)
assetID: 3ed87fc1-734c-d8b5-d908-0ae3359769ed
permalink: en-us/docs/xboxlive/rest/json-gameclipthumbnail.html
author: KevinAsgari
description: " GameClipThumbnail (JSON)"
ms.author: kevinasg
ms.date: 20-12-2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Xbox Live, Xbox, ゲーム, UWP, Windows 10, Xbox One
ms.localizationpriority: medium
ms.openlocfilehash: 681a269cd861f741e2bbde3554acc1b25104d90d
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2018
ms.locfileid: "4082676"
---
# <a name="gameclipthumbnail-json"></a>GameClipThumbnail (JSON)
個々 のサムネイルに関連する情報が含まれています。 1 つのクリップを複数のサイズが存在することができ、表示用の適切なものを選択するクライアントがします。 
<a id="ID4EN"></a>

 
## <a name="gameclipthumbnail"></a>GameClipThumbnail
 
GameClipThumbnail オブジェクトでは、次の仕様があります。
 
| メンバー| 種類| 説明| 
| --- | --- | --- | 
| <b>uri</b>| string| サムネイル画像の URI。| 
| <b>fileSize</b>| 32 ビット符号なし整数| サムネイル画像のファイルの合計サイズ。| 
| <b>thumbnailType</b>| ThumbnailType| サムネイル画像の種類です。| 
  
<a id="ID4EAC"></a>

 
## <a name="sample-json-syntax"></a>JSON 構文の例
 

```json
{
         "uri": "http://gameclips.xbox.com/thumbnails/7ce5c1a7-1255-46d3-a90e-34a0e2dfab06/small.jpg",
         "fileSize": 123,
         "width": 120,
         "height": 250
       }
    
```

  
<a id="ID4EJC"></a>

 
## <a name="see-also"></a>関連項目
 
<a id="ID4ELC"></a>

 
##### <a name="parent"></a>Parent 

[JavaScript Object Notation (JSON) オブジェクト リファレンス](atoc-xboxlivews-reference-json.md)

   