---
title: quotaInfo (JSON)
assetID: 3147ab78-e671-e142-0a3a-688dc4079451
permalink: en-us/docs/xboxlive/rest/json-quota.html
author: KevinAsgari
description: " quotaInfo (JSON)"
ms.author: kevinasg
ms.date: 20-12-2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Xbox Live, Xbox, ゲーム, UWP, Windows 10, Xbox One
ms.localizationpriority: medium
ms.openlocfilehash: 4308c148a530233e06d666da5ec446821ba6ee26
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2018
ms.locfileid: "4088414"
---
# <a name="quotainfo-json"></a>quotaInfo (JSON)
クォータ タイトル グループについてを説明します。 
<a id="ID4EN"></a>

 
## <a name="quotainfo"></a>quotaInfo
 
QuotaInfo オブジェクトには、次の仕様があります。
 
グローバル ストレージ
 
| メンバー| 種類| 説明| 
| --- | --- | --- | 
| quotaBytes| 32 ビット符号付き整数 | タイトルで使用可能なバイトの最大数。| 
| usedBytes| 32 ビット符号付き整数 | タイトルで使用されるバイト数。| 
  
<a id="ID4EXB"></a>

 
## <a name="sample-json-syntax"></a>JSON 構文の例
 
次の例は、グローバル ストレージへの応答を示しています。
 

```json
{
    "quotaInfo":
    {
        "usedBytes":4194304,
        "quotaBytes":536870912
    }
}
      
```

  
<a id="ID4ECC"></a>

 
## <a name="see-also"></a>関連項目
 
<a id="ID4EEC"></a>

 
##### <a name="parent"></a>Parent 

[JavaScript Object Notation (JSON) オブジェクト リファレンス](atoc-xboxlivews-reference-json.md)

   