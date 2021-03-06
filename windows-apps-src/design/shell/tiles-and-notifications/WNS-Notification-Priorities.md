---
title: WNS 通知の優先度
description: 通知に設定できるさまざまな優先順位の説明
ms.date: 01/10/2017
ms.topic: article
keywords: windows 10、uwp、WinRT API、WNS
localizationpriority: medium
ms.openlocfilehash: 3310b34b2748bd684e46e04775c973680f8e03a9
ms.sourcegitcommit: 445320ff0ee7323d823194d4ec9cfa6e710ed85d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282240"
---
# <a name="wns-notification-priorities"></a>WNS 通知の優先度
単純なヘッダーを使用してメッセージを WNS に送信することで、通知の優先順位を設定することにより、バッテリを使用する状況での通知の配信方法を制御できます。

## <a name="power-on-windows"></a>Windows での電源をオンにする
バッテリ駆動デバイスでのみ作業しているユーザーが増えるにつれて、電力使用量を最小限に抑えることが、すべてのアプリの標準要件になりました。 アプリが提供する値よりも多くのエネルギーを消費する場合、ユーザーはアプリをアンインストールする可能性があります。 Windows オペレーティングシステムでは、可能な限りバッテリの電力使用量が削減されますが、アプリは効率的に作業する必要があります。 

WNS の優先順位は、重要でない作業をバッテリから移動する方法の1つです。 WNS の優先順位は、どの通知を即座に配信する必要があるかをシステムに通知し、デバイスが電源に接続されるまで待機することができます。 これらのヒントを使用すると、システムはユーザーとアプリの両方にとって最も重要な時間で通知を配信できます。 

## <a name="power-modes-on-the-device"></a>デバイスの電源モード
すべての Windows デバイスは、さまざまな電源モード (バッテリ、バッテリ節約、および充電) を介して動作し、ユーザーはさまざまな電源モードのアプリから異なる動作を期待しています。 デバイスがオンになっている場合は、すべての通知が配信されます。 バッテリ節約モードでは、最も重要な通知のみを配信する必要があります。 デバイスが接続されている間、同期または非時間の重要な操作を完了できます。

Windows では、どのユーザーまたはアプリケーションにとって重要な通知が認識されないので、システムはアプリ全体に依存して、通知に適切な優先順位を設定します。 

## <a name="priorities"></a>主
アプリでプッシュ通知を送信するときに使用できる優先順位は4つあります。 優先順位は個々の通知に対して設定されます。これにより、インスタントメッセージ (IM メッセージなど) を即座に配信する必要がある通知を選択できます。また、連絡先の写真更新プログラムなどを選択できます。

優先順位は次のとおりです。 

|    [Priority]    |    ユーザーのオーバーライド    |    説明    |    例    |
|----------------|---------------------|-------------------|---------------|
|    高    |    はい–ユーザーはアプリからのすべての通知をブロックしたり、アプリがバッテリ節約モードで調整されないようにしたりすることができます。    |    デバイスが通知を受信できるときに、すぐに配信する必要がある最も重要な通知。 デバイスをスリープ解除する必要がある VoIP 通話や重大なアラートなどは、このカテゴリに分類されます。    |    VoIP 通話、タイムクリティカルなアラート    |
|    Medium    |    はい–ユーザーはアプリからのすべての通知をブロックしたり、アプリがバッテリ節約モードで調整されないようにしたりすることができます。    |    これらは重要なことではなく、すぐに実行する必要がないものですが、ユーザーがバックグラウンドで実行されていない場合は annoyed になります。    |    セカンダリ電子メールアカウントの同期、ライブタイルの更新。    |
|    Low    |    はい–ユーザーはアプリからのすべての通知をブロックしたり、アプリがバッテリ節約モードで調整されないようにしたりすることができます。    |    ユーザーがデバイスを使用している場合、またはバックグラウンドアクティビティが理にかなっている場合にのみ意味を持つ通知。 これらはキャッシュされ、ユーザーがデバイスにサインインまたは接続するまで処理されません。    |    連絡先の状態 (オンラインまたはオフライン)    |
|    非常に低い     |    いいえ-低優先度の通知がバッテリ節約モードで調整されるのを防ぐことはできません。    |    これは、ユーザーがバッテリ節約ポリシーを上書きできないことを除いて、低優先度とほぼ同じです。 これらの通知は、バッテリ節約時には配信されません。    |    同期サービスのファイルを同期しています。    |

多くのアプリは、ライフサイクル全体で異なる優先度の通知を受け取ることに注意してください。 優先度は通知ごとに設定されるため、これは問題ではありません。 VoIP アプリは、着信呼び出しに対して高優先度の通知を送信し、連絡先がオンラインになったときに優先度の低い通知を受け取ることができます。 

## <a name="setting-the-priority"></a>優先順位の設定

通知要求の優先順位の設定は、POST 要求の追加ヘッダーを使用して行われます (`X-WNS-PRIORITY`)。 これは、優先度にマップされる 1 ~ 4 の整数値です。 

| 優先順位名 | X-WNS-優先順位値 | の既定値: |
|---------------|----------------------|------------------|
| 高 | 1 | トースト |
| 重要 | 2 | タイルとバッジ |
| Low | 3 | 直接 |
| 非常に低い | 4 |  |

下位互換性を確保するために、優先順位を設定する必要はありません。 アプリが通知の優先順位を設定していない場合、システムは既定の優先順位を指定します。 上の図には既定値が表示されており、Windows の既存のバージョンの動作と一致しています。 

## <a name="detailed-listing-of-desktop-behavior"></a>デスクトップ動作の詳細な一覧 

Windows のさまざまな Sku でアプリを配布する場合は、通常、上記のセクションのグラフに従うことをお勧めします。 

各優先度の推奨される動作を次に示します。 これは、各デバイスがグラフに正確に対応していることを保証するものではありません。 Oem は、動作の構成を自由に行うことができますが、ほとんどはこのグラフの近くにあります。 

| デバイスの状態    | 的高    |    的Medium        | 的Low    |    的非常に低い    |
|-------------------------------------------------------|----------------------------------------------------|----------------------------------------------------|----------------------------------------------------|--------------------------|
|    画面上または電源に接続されている    |    高める    |    高める    |    高める    |    高める    |
|    画面をオフにしてバッテリを長持ちさせる    |    高める    |    ユーザーが除外する場合: 他のユーザーを配信: batch     |    ユーザーが除外する場合: 他のものを配信: キャッシュ *    |    キャッシュ    |
|    バッテリ節約の有効化    |    ユーザーが除外する場合: 他のものを配信する場合: キャッシュ    |    ユーザーが除外する場合: 他のものを配信する場合: キャッシュ    |    ユーザーが除外する場合: 他のものを配信する場合: キャッシュ    |    キャッシュ     |
|    バッテリ使用時 + バッテリ節約有効 + 画面オフ    |    ユーザーが除外する場合: 他のものを配信する場合: キャッシュ    |    ユーザーが除外する場合: 他のものを配信する場合: キャッシュ    |    ユーザーが除外する場合: 他のものを配信する場合: キャッシュ    |    キャッシュ    |

低優先度の通知は、既定では Windows Phone ベースのデバイスの画面オフとバッテリのみで配信されることに注意してください。 これは、既存の MPNS ポリシーとの互換性を維持するためのものです。 また、4番目と5番目の行は同じであるため、さまざまなシナリオを呼び出します。

バッテリ節約のためにアプリを除外するには、ユーザーは [設定] の [アプリ別バッテリ使用量] にアクセスし、[アプリにバックグラウンドタスクの実行を許可する] を選択する必要があります。 このユーザー選択により、高、中、低優先度の通知に対して、アプリをバッテリ節約から除外さします。 また、 [BACKGROUNDEXECUTIONMANAGER API](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccesskindasync#Windows_ApplicationModel_Background_BackgroundExecutionManager_RequestAccessKindAsync_Windows_ApplicationModel_Background_BackgroundAccessRequestKind_System_String_)を呼び出して、ユーザーのアクセス許可をプログラムで確認することもできます。  

## <a name="related-topics"></a>関連トピック
- [Windows プッシュ通知サービス (WNS) の概要](windows-push-notification-services--wns--overview.md)
- [バックグラウンドで実行するためのアクセス許可を要求しています](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccesskindasync#Windows_ApplicationModel_Background_BackgroundExecutionManager_RequestAccessKindAsync_Windows_ApplicationModel_Background_BackgroundAccessRequestKind_System_String_)
