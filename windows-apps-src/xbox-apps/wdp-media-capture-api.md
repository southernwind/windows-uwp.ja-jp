---
author: WilliamsJason
title: メディア キャプチャ API のリファレンス
description: メディア キャプチャ API にプログラムでアクセスする方法について説明します。
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP
ms.assetid: 3f92c8fd-4096-4972-97da-01ae5db6423c
ms.openlocfilehash: 9236b0cd9ac658a34283e54ba70b7e70d19c6bb3
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.locfileid: "244504"
---
# <a name="media-capture-api-reference"></a>メディア キャプチャ API のリファレンス #

**要求**

次の要求形式を使用して、現在の画面の PNG 画像を取得できます。

| メソッド        | 要求 URI     | 
| ------------- |-----------------|
| GET           | /ext/screenshot |
<br>

**URI パラメーター**

次の追加パラメーターを要求 URI に指定できます。


| URI パラメーター      | 説明     | 
| ------------------ |-----------------|
| download (省略可能)| ホスト ブラウザーでスクリーンショットをブラウザーにレンダリングするのではなく添付ファイルとしてダウンロードする必要があることを、HTTP 応答ヘッダーで設定する必要があるかどうかを示すブール値。  |
<br>

**要求ヘッダー**

* なし

**要求本文**

* なし

###<a name="response"></a>応答###

**状態コード**

この API では次の状態コードが返される可能性があります。

| HTTP 状態コード   | 説明     | 
| ------------------ |-----------------|
| 200                | スクリーンショットの要求が成功し、キャプチャが返される |
| 5XX                | 予期しないエラーのエラー コード |
<br>

**利用可能なデバイス ファミリ**

* Windows Xbox
