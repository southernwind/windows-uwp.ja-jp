---
title: 広告キャンペーン レポート
ms.assetid: 8C5907A6-8059-4CAF-951F-C97301A5EEDF
description: パートナーセンターの Ad キャンペーンレポートを使用すると、アプリの昇格によって ad キャンペーンがどのように実行されているかを確認できます。
ms.date: 10/31/2018
ms.topic: article
keywords: Windows 10, UWP, 販売促進, アプリ, キャンペーン, レポート, インストール
ms.localizationpriority: medium
ms.openlocfilehash: fef1c148db8a1c53be6c7ffd15cd8cd2992868ee
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260066"
---
# <a name="ad-campaign-report"></a>広告キャンペーン レポート

[パートナーセンター](https://partner.microsoft.com/dashboard)の**ad キャンペーン**レポートを使用すると、アプリの昇格によって[ad キャンペーン](create-an-ad-campaign-for-your-app.md)がどのように実行されているかを確認できます。 レポートを表示するには、左側のナビゲーションメニューで **[惹き]** を展開し、 **[Ad キャンペーン]** を選択します。

## <a name="definitions"></a>定義

このレポートは、次の項目のデータを示します。

-   **[インプレッション数]** : 広告がユーザーに表示された回数です。
-   **[クリック数]** : 広告がユーザーによってクリックされた回数です。
-   **[コンバージョン]** : アプリのインストール数の増加をキャンペーンの目的にしている場合、コンバージョンはユーザーが広告を表示してから 24 時間以内にアプリをインストールした回数です。 アプリのエンゲージメント向上をキャンペーンの目的にしている場合、コンバージョンはユーザーが広告を表示してから 24 時間以内にアプリを開いた回数です。 インストールの追跡とコンバージョンの測定方法について詳しくは、以下をご覧ください。
-   **[支出]** : 各キャンペーンに費やした費用の合計です。

## <a name="apply-filters"></a>フィルターの適用

レポートの上部にある **[セクション フィルター]** を使ってレポートに表示されるデータの範囲を調整できます。

-   **[日付]** : 既定では **[過去 30 日間]** が選択されていますが、3、6、12 か月間のデータを表示することもできます。
-   **[キャンペーンの目的]** : すべてのキャンペーンを表示することも、目的が **[アプリのインストール数]** または **[アプリの利用率]** であるキャンペーンのみを表示するようにデータを絞り込むこともできます。
-   **[アプリ名]** : 既定の選択は **[すべて]** ですが、特定の 1 つのアプリのキャンペーンのみを表示することもできます。
-   **[キャンペーンの種類]** : すべてのキャンペーンの種類を表示することも、有料広告、自社広告、またはコミュニティ広告キャンペーンのみを表示するようにデータを絞り込むこともできます。
-   **[状態]** : 既定の選択は **[すべて]** ですが、特定の状態 ( **[アクティブ]** 、 **[ドラフト]** 、 **[一時停止]** 、 **[終了]** 、 **[要注意]** ) であるキャンペーンのデータのみを表示することもできます。


## <a name="ad-campaign-metrics"></a>広告キャンペーンのメトリック

ページの上の方に、広告キャンペーンの一覧が表示されます。ここには、各広告キャンペーンのインプレッション数、クリック数、コンバージョン数と合わせて、支出合計と実施できるアクションが表示されます。 キャンペーンを編集するには、この一覧で名前をクリックします。

ページの下の方には、これらのパフォーマンスのメトリックがグラフにプロットされた線として表示されます。 前述のとおり、 **[インプレッション数]** 、 **[クリック数]** 、 **[コンバージョン]** 、 **[支出]** のタブ見出しを選んで、各種データを表示します。

一度に最大 6 つの異なる広告キャンペーンのパフォーマンス データを表示できます。 **[多くのキャンペーン]** を選択して、表示するキャンペーンを選びます。 表示されているキャンペーンの横にあるマイナス記号を選択して、そのキャンペーンをグラフから削除することもできます。


## <a name="install-tracking"></a>インストールの追跡

パートナーセンターから ad キャンペーンのインストールを実行すると、アプリを提供するために必要な公開が非常に多くなります。 広告インプレッションは、アプリに関心を持っている可能性が最も高いと思われるユーザーに表示され、ユーザーは広告をクリックして、ストアからアプリをインストールします。 以前は、広告キャンペーンの結果行われたインストールと、その他のソースに由来するインストールを区別するのは困難でした。

このレポートには、広告キャンペーンを実行したことで増加したインストール数が表示されます。 これは、広告キャンペーンの直接的な結果として発生したダウンロード数のみを表しており、他のソースからのダウンロードは含まれません。

広告キャンペーンのインストール数を監視することで、アプリの宣伝に費やした費用について真の投資効果を測定できます。 また、新規ユーザーを獲得するコストと、ユーザーの生涯にわたる価値を比較することもできます。


## <a name="measuring-conversions"></a>コンバージョンの測定

広告キャンペーンにより、他のアプリ内で広告が表示されます。 広告が表示されたユーザーは、広告をクリックするか、表示された広告を見たことにより、アプリをインストールする可能性があります。

ユーザーに広告が表示され、広告をクリックするかアプリのストア登録情報ページに移動して 24 時間以内にアプリをインストールした場合、そのインストールは、インプレッションを提供したキャンペーンに基づいて行われたと見なされます。

インストールは、電話、タブレット、PC、その他の Windows 10 デバイス間で、アプリをインストールしたユーザーに基づいて、ストアで追跡されます。 広告エンジンは広告を表示したユーザーを追跡し、この情報を使って、広告を表示したユーザーを、アプリをインストールしたユーザーに関連付けます。 アプリのインストールをカウントするためには、いくつかの要件を満たす必要があります。

1.  ユーザーは対象から除外されるように設定していない。
2.  ユーザーは Microsoft アカウントにログインしている。
3.  ユーザーは [COPPA](https://www.ftc.gov/enforcement/rules/rulemaking-regulatory-reform-proceedings/childrens-online-privacy-protection-rule) 要件を満たしている (COPPA 要件を満たしていないユーザーを追跡することはできません)。

この結果、アプリのインストールの追跡で、広告キャンペーンによって生じた実際のインストール数より少なく報告される可能性があります。 パートナーセンターの[購入](acquisitions-report.md)レポートでは、(キャンペーンを通じて、またはそれ以外の方法で) アプリのインストールの合計数を表示できることに注意してください。


## <a name="account-billing-history"></a>アカウントの支払い履歴

アカウントに関連付けられたすべての広告キャンペーンのトランザクションを表示するには、左側のナビゲーション メニューで **[請求履歴]** を選択します。

トランザクションごとに、 **[処理日]** 、適切な **[キャンペーン名]** 、料金の **[支払い方法]** 、 **[支払い ID]** 、 **[請求開始日]** 、 **[請求終了日]** 、料金の **[合計金額]** 、および **[入金状況]** が表示されます。

**[ダウンロード]** リンクをクリックすると、アカウントの支払い履歴を Microsoft Word 文書としてダウンロードできます。

## <a name="related-topics"></a>関連トピック

* [アプリの広告キャンペーンを作成する](create-an-ad-campaign-for-your-app.md)

 

 
