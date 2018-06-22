---
title: リソース
description: リソースは、Direct3D パイプラインからアクセスできるメモリ内の領域です。
ms.assetid: 2E68E5A8-83DA-4DC8-B7F3-B8988CF8090C
keywords:
- リソース
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: b4984ca7ffb434123a13a695e8c74622c6b270f4
ms.sourcegitcommit: 0ab8f6fac53a6811f977ddc24de039c46c9db0ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2018
ms.locfileid: "1653671"
---
# <a name="resources"></a>リソース


リソースは、Direct3D パイプラインからアクセスできるメモリ内の領域です。 パイプラインでメモリに効率的にアクセスするには、パイプラインに渡すデータ (入力ジオメトリ、シェーダー リソース、テクスチャなど) をリソースに格納する必要があります。 すべての Direct3D リソースの派生元となるリソースは 2 種類あります。バッファーとテクスチャです。 各パイプライン ステージでは最大 128 個のリソースをアクティブにできます。

通常、アプリケーションごとに多数のリソースが作成されます。 リソースの例として、頂点バッファー、インデックス バッファー、定数バッファー、テクスチャ、シェーダー リソースなどがあります。 リソースの使用方法を決めるオプションはいくつか存在します。 厳密に型指定されたリソースを作成するか、型指定のないリソースを作成することができます。また、リソースで読み取りアクセスと書き込みアクセスの両方を可能にするかどうかも制御できます。さらに、CPU のみ、GPU のみ、またはその両方からリソースにアクセスできるようにすることも可能です。 当然ながら、速度と機能はトレードオフの関係にあります。つまり、多くの機能をリソースに許可するほど、予想されるパフォーマンスが低下します。

アプリケーションでは多数のテクスチャを使用することが多いので、Direct3D にはテクスチャ管理を簡略化するテクスチャ配列という概念も用意されています。 テクスチャ配列には (型とサイズがすべて同じである) 1 つ以上のテクスチャが格納されており、アプリケーション内から、またはシェーダーでインデックスを作成できます。 テクスチャ配列により、複数のインデックスを持つ 1 つのインターフェイスを使用して多数のテクスチャにアクセスすることができます。 さまざまなテクスチャの種類を管理するために、テクスチャ配列は必要なだけ作成可能です。

アプリケーションで使用するリソースを作成したら、それらを使用するパイプライン ステージに各リソースを接続またはバインドします。 これは、リソースへのポインターを受け取るバインド API を呼び出すことで実行します。 複数のパイプライン ステージで同じリソースにアクセスしなければならない場合があるため、Direct3D にはリソース ビューという概念が用意されています。 ビューは、リソースのうち、アクセス可能な部分を識別します。 *m* 個のビューまたは 1 つのリソースを作成して、*n* 個のパイプライン ステージにバインドすることができます。この際、共有リソースのバインド規則に従うことが前提となります (従わなければコンパイル時にランタイムでエラーが発生します)。

リソース ビューは、リソース (テクスチャやバッファーなど) にアクセスするための一般的なモデルです。 ビューを使用すると、アクセスするデータやデータへのアクセス方法をランタイムに対して指示できます。そのため、リソース ビューを使うことで、型指定なしのリソースを作成できるようになります。 つまり、コンパイル時に特定のサイズのリソースを作成し、その後、リソースがパイプラインにバインドされるときにリソース内でデータ型を宣言できます。 ビューは、リソースを使用するための多数の機能を公開します。たとえば、シェーダーで深度/ステンシル サーフェスを読み返して、1 つのパスで動的なキューブマップを生成し、ボリュームの複数のスライスに同時にレンダリングすることができます。

## <a name="span-idin-this-sectionspanin-this-section"></a><span id="in-this-section"></span>このセクションの内容


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">トピック</th>
<th align="left">説明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="resource-types.md">リソースの種類</a></p></td>
<td align="left"><p>リソースの種類によって、レイアウト (またはメモリ使用量) はそれぞれ異なります。 Direct3D パイプラインで使用されるリソースはすべて、2 つの基本的なリソースの種類である<a href="resource-types.md#buffer-resources">バッファー</a>と<a href="resource-types.md#texture-resources">テクスチャ</a>から派生したものです。 バッファーは未加工データ (要素) のコレクションで、テクスチャはテクセル (テクスチャ要素) のコレクションです。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="choosing-a-resource.md">リソースの選択</a></p></td>
<td align="left"><p>リソースは、3D パイプラインで使用されるデータのコレクションです。 リソースを作成してその動作を定義することが、アプリケーションをプログラミングするための第一歩になります。 このガイドでは、アプリケーションで必要なリソースの選択に関する基本的なトピックについて説明します。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="copying-and-accessing-resource-data.md">リソース データのコピーとアクセス</a></p></td>
<td align="left"><p>使用法フラグは、アプリケーションがリソース データをどのように使用するかを示し、リソースを可能な限りメモリのパフォーマンスの高い領域に配置します。 リソース データはリソース間でコピーされ、CPU または GPU がパフォーマンスに影響を与えることなくリソースにアクセスできるようにします。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="texture-views.md">テクスチャ ビュー</a></p></td>
<td align="left"><p>Direct3D では、ビューを使ってテクスチャ リソースにアクセスします。ビューは、メモリ内のリソースをハードウェアで解釈するためのメカニズムです。 ビューを使うと、アプリケーションで要求される表現で、特定のパイプライン ステージから必要な<a href="resource-types.md">サブリソース</a>だけにアクセスできるようになります。</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>関連トピック


[座標系](coordinate-systems.md)

[Direct3D グラフィックスの学習ガイド](index.md)

[浮動小数点の規則](floating-point-rules.md)

[データ型の変換](data-type-conversion.md)