---
title: "線リスト"
description: "線リストとは、独立した直線セグメントの一覧を指します。 線リストは、3D シーンに氷雨や豪雨を追加する場合などに便利です。 アプリケーションは、頂点の配列を入力することで線リストを作成します。"
ms.assetid: 42BF32A1-3535-42A3-82C5-3945CB309F2C
keywords: "線リスト"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: 8ebe6c67bd6b68023f59599cdcbd1f8f44f80e84
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="line-lists"></a>線リスト


線リストとは、独立した直線セグメントの一覧を指します。 線リストは、3D シーンに氷雨や豪雨を追加する場合などに便利です。 アプリケーションは、頂点の配列を入力することで線リストを作成します。 線リストの頂点の数は、2 以上の偶数でなければならない点に注意してください。

-   [例](#example)
-   [関連トピック](#related-topics)

## <a name="span-idexamplespanspan-idexamplespanspan-idexamplespanexample"></a><span id="Example"></span><span id="example"></span><span id="EXAMPLE"></span>例


次の図は、レンダリングされた線リストを示しています。

![線リストの図](images/linelst.png)

線リストには、素材やテクスチャを適用できます。 素材やテクスチャの色は描画された線にのみ表示され、線の間の点には表示されません。

次のコードは、この線リストの頂点の作成方法を示しています。

```
struct CUSTOMVERTEX
{
    float x,y,z;
};

CUSTOMVERTEX Vertices[] = 
{
    {-5.0, -5.0, 0.0},
    { 0.0,  5.0, 0.0},
    { 5.0, -5.0, 0.0},
    {10.0,  5.0, 0.0},
    {15.0, -5.0, 0.0},
    {20.0,  5.0, 0.0}
};
```

次のコード例は、線リストを Direct3D でレンダリングする方法を示しています。

```
//
// It is assumed that d3dDevice is a valid
// pointer to an IDirect3DDevice interface.
//
d3dDevice->DrawPrimitive( D3DPT_LINELIST, 0, 3 );
```

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>関連トピック


[プリミティブ](primitives.md)

 

 



