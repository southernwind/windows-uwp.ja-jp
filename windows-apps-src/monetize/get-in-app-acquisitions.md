---
ms.assetid: 1599605B-4243-4081-8D14-40F6F7734E25
description: 日付範囲やその他のオプション フィルターを指定して、アドオンに関する集計入手データを取得するには、Microsoft Store 分析 API の以下のメソッドを使います。
title: アドオンの入手数の取得
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, Store サービス, Microsoft Store 分析 API, アドオンの入手数
ms.localizationpriority: medium
ms.openlocfilehash: cd7e907994943dbce83d195e80a15770833f7e4b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57650987"
---
# <a name="get-add-on-acquisitions"></a>アドオンの入手数の取得

日付範囲やその他のオプション フィルターを指定して、アプリのアドオンに関する集計入手データを JSON 形式で取得するには、Microsoft Store 分析 API の以下のメソッドを使います。 この情報も記載されて、[アドオン買収レポート](../publish/add-on-acquisitions-report.md)パートナー センターでします。

## <a name="prerequisites"></a>前提条件

このメソッドを使うには、最初に次の作業を行う必要があります。

* Microsoft Store 分析 API に関するすべての[前提条件](access-analytics-data-using-windows-store-services.md#prerequisites)を満たします (前提条件がまだ満たされていない場合)。
* このメソッドの要求ヘッダーで使う [Azure AD アクセス トークンを取得](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token)します。 アクセス トークンを取得した後、アクセス トークンを使用できるのは、その有効期限が切れるまでの 60 分間です。 トークンの有効期限が切れたら新しいトークンを取得できます。

## <a name="request"></a>要求


### <a name="request-syntax"></a>要求の構文

| メソッド | 要求 URI                                                                |
|--------|----------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions``` |


### <a name="request-header"></a>要求ヘッダー

| Header        | 種類   | 説明          |
|---------------|--------|--------------|
| Authorization | string | 必須。 **Bearer** &lt;*トークン*&gt; という形式の Azure AD アクセス トークン。 |


### <a name="request-parameters"></a>要求パラメーター

*applicationId* または *inAppProductId* パラメーターが必要です。 アプリに登録されたすべてのアドオンの入手データを取得するには、*applicationId* パラメーターを指定します。 単一のアドオンの入手データを取得するには、*inAppProductId* パラメーターを指定します。 両方を指定した場合、*applicationId* パラメーターは無視されます。

| パラメーター        | 種類   |  説明      |  必須  
|---------------|--------|---------------|------|
| applicationId | string | アドオン入手データを取得するアプリの [Store ID](in-app-purchases-and-trials.md#store-ids) です。  |  〇  |
| inAppProductId | string | 入手データを取得するアドオンの [Store ID](in-app-purchases-and-trials.md#store-ids) です。  | 〇  |
| startDate | date | 取得するアドオン入手データの日付範囲の開始日です。 既定値は現在の日付です。 |  X  |
| endDate | date | 取得するアドオン入手データの日付範囲終了日です。 既定値は現在の日付です。 |  X  |
| top | int | 要求で返すデータの行数です。 最大値および指定しない場合の既定値は 10000 です。 クエリにこれを上回る行がある場合は、応答本文に次リンクが含まれ、そのリンクを使ってデータの次のページを要求できます。 |  X  |
| skip | int | クエリでスキップする行数です。 大きなデータ セットを操作するには、このパラメーターを使用します。 たとえば、top=10000 と skip=0 を指定すると、データの最初の 10,000 行が取得され、top=10000 と skip=10000 を指定すると、データの次の 10,000 行が取得されます。 |  X  |
| filter |string  | 応答内の行をフィルター処理する 1 つまたは複数のステートメントです。 詳しくは、次の「[フィルター フィールド](#filter-fields)」セクションをご覧ください。 | いいえ   |
| aggregationLevel | string | 集計データを取得する時間範囲を指定します。 次のいずれかの文字列を指定できます。<strong>day</strong>、<strong>week</strong>、または <strong>month</strong>。 指定しない場合、既定値は <strong>day</strong> です。 | X |
| orderby | string | それぞれのアドオン入手数について結果データ値の順序を指定するステートメントです。 構文は <em>orderby=field [order],field [order],...</em> です。<em>field</em> パラメーターには、次のいずれかの文字列を指定できます。<ul><li><strong>date</strong></li><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>ストア クライアント</strong></li><li><strong>性別</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName</strong></li></ul><p><em>order</em> パラメーターは省略可能であり、<strong>asc</strong> または <strong>desc</strong> を指定して、各フィールドを昇順または降順にすることができます。 既定値は <strong>asc</strong> です。</p><p><em>orderby</em> 文字列の例: <em>orderby=date,market</em></p> |  X  |
| groupby | string | 指定したフィールドのみにデータ集計を適用するステートメントです。 次のフィールドを指定できます。<ul><li><strong>date</strong></li><li><strong>ApplicationName</strong></li><li><strong>inAppProductName</strong></li><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>ストア クライアント</strong></li><li><strong>性別</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName</strong></li></ul><p>返されるデータ行には、<em>groupby</em> パラメーターに指定したフィールドと次の値が含まれます。</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>inAppProductId</strong></li><li><strong>取得数</strong></li></ul><p><em>groupby</em> パラメーターは、<em>aggregationLevel</em> パラメーターと同時に使用できます。 例: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  X  |


### <a name="filter-fields"></a>フィルター フィールド

要求の *filter* パラメーターには、応答内の行をフィルター処理する 1 つまたは複数のステートメントが含まれます。 各ステートメントには **eq** 演算子または **ne** 演算子と関連付けられるフィールドと値が含まれ、**and** または **or** を使ってステートメントを組み合わせることができます。 *filter* パラメーターの例を次に示します。

-   *フィルター = 市場 eq '米国'] と [性別の eq います '*
-   *フィルター (米国' 市場 ne) = し、(性別 ne '不明') と (性別 ne います ') と (市場 ne 'NO') と (ageGroup ne '55 より大きい' または ageGroup ne 'より小さい 13')*

サポートされているフィールドの一覧については、次の表をご覧ください。 *filter* パラメーターでは、文字列値を単一引用符で囲む必要があります。

| フィールド        |  説明        |
|---------------|-----------------|
| acquisitionType | 次のいずれかの文字列です。<ul><li><strong>無料</strong></li><li><strong>試用版</strong></li><li><strong>有料</strong></li><li><strong>プロモーション コード</strong></li><li><strong>Iap</strong></li></ul> |
| ageGroup | 次のいずれかの文字列です。<ul><li><strong>小さい 13</strong></li><li><strong>13 ~ 17 日</strong></li><li><strong>18 ~ 24</strong></li><li><strong>25 34</strong></li><li><strong>35 44</strong></li><li><strong>44 55</strong></li><li><strong>55 より大きい</strong></li><li><strong>Unknown</strong></li></ul> |
| storeClient | 次のいずれかの文字列です。<ul><li><strong>Windows Phone ストア (クライアント)</strong></li><li><strong>Microsoft Store (クライアント)</strong></li><li><strong>Microsoft Store (web)</strong></li><li><strong>組織でボリューム購入</strong></li><li><strong>その他</strong></li></ul> |
| gender | 次のいずれかの文字列です。<ul><li><strong>m</strong></li><li><strong>f</strong></li><li><strong>Unknown</strong></li></ul> |
| market | 入手が発生した市場の ISO 3166 国コードを含む文字列です。 |
| osVersion | 次のいずれかの文字列です。<ul><li><strong>Windows Phone 7.5</strong></li><li><strong>Windows Phone 8</strong></li><li><strong>Windows Phone 8.1</strong></li><li><strong>Windows Phone 10</strong></li><li><strong>Windows 8</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows 10</strong></li><li><strong>Unknown</strong></li></ul> |
| deviceType | 次のいずれかの文字列です。<ul><li><strong>PC</strong></li><li><strong>Phone</strong></li><li><strong>Console</strong></li><li><strong>IoT</strong></li><li><strong>Holographic</strong></li><li><strong>Unknown</strong></li></ul> |
| orderName | アドオンを入手するために使用されたプロモーション コードの注文の名前を指定する文字列です (このフィールドは、ユーザーがプロモーション コードを利用してアドオンを入手した場合のみに適用されます)。 |


### <a name="request-example"></a>要求の例

アドオン入手データを取得するためのいくつかの要求の例を次に示します。 *inAppProductId* および *applicationId* の値を、アドオンまたはアプリの適切なストア ID に置き換えてください。

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?inAppProductId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?inAppProductId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=7/3/2015&top=100&skip=0&filter=market ne 'US' and gender ne 'Unknown' and gender ne 'm' and market ne 'NO' and ageGroup ne '>55' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>応答


### <a name="response-body"></a>応答本文

| Value      | 種類   | 説明         |
|------------|--------|------------------|
| Value      | array  | 集計アドオン入手データが含まれるオブジェクトの配列です。 各オブジェクトのデータについて詳しくは、次の「[アドオン入手値](#add-on-acquisition-values)」セクションをご覧ください。                                                                                                              |
| @nextLink  | string | データの追加ページがある場合、この文字列には、データの次のページを要求するために使用できる URI が含まれます。 たとえば、要求の **top** パラメーターが 10000 に設定されたが、クエリのアドオン入手データに 10,000 を超える行が含まれている場合に、この値が返されます。 |
| TotalCount | int    | クエリの結果データ内の行の総数です。    |


<span id="add-on-acquisition-values" />

### <a name="add-on-acquisition-values"></a>アドオン入手値

*Value* 配列の要素には、次の値が含まれます。

| Value               | 種類    | 説明        |
|---------------------|---------|---------------------|
| date                | string  | 入手データの日付範囲の最初の日付です。 要求に日付を指定した場合、この値はその日付になります。 要求に週、月、またはその他の日付範囲を指定した場合、この値はその日付範囲の最初の日付になります。 |
| inAppProductId      | string  | 入手データを取得するアドオンのストア ID です。                                                                                                                                                                 |
| inAppProductName    | string  | アドオンの表示名です。 この値は、*aggregationLevel* パラメーターが **day** に設定されている場合にのみ応答データに表示されます (ただし、*groupby* パラメーターに **inAppProductName** フィールドを指定していない場合)。                                                                                                                                                                                                            |
| applicationId       | string  | アドオン入手データを取得するアプリのストア ID です。                                                                                                                                                           |
| applicationName     | string  | アプリの表示名です。                                                                                                                                                                                                             |
| deviceType          | string  | 入手を完了したデバイスの種類です。 サポートされる文字列の一覧については、前の「[フィルター フィールド](#filter-fields)」セクションをご覧ください。                                                                                                  |
| orderName           | string  | 注文の名前。                                                                                                                                                                                                                   |
| storeClient         | string  | 入手が発生したストアのバージョンです。 サポートされる文字列の一覧については、前の「[フィルター フィールド](#filter-fields)」セクションをご覧ください。                                                                                            |
| osVersion           | string  | 入手が発生した OS のバージョンです。 サポートされる文字列の一覧については、前の「[フィルター フィールド](#filter-fields)」セクションをご覧ください。                                                                                                   |
| market              | string  | 入手が発生した市場の ISO 3166 国コードです。                                                                                                                                                                  |
| gender              | string  | 入手を行ったユーザーの性別です。 サポートされる文字列の一覧については、前の「[フィルター フィールド](#filter-fields)」セクションをご覧ください。                                                                                                    |
| ageGroup            | string  | 入手を行ったユーザーの年齢グループです。 サポートされる文字列の一覧については、前の「[フィルター フィールド](#filter-fields)」セクションをご覧ください。                                                                                                 |
| acquisitionType     | string  | 入手の種類です (無料、有料など)。 サポートされる文字列の一覧については、前の「[フィルター フィールド](#filter-fields)」セクションをご覧ください。                                                                                                    |
| acquisitionQuantity | 整数 | 発生した入手の数です。                        |


### <a name="response-example"></a>応答の例

この要求の JSON 返信の本文の例を次に示します。

```json
{
  "Value": [
    {
      "date": "2015-01-02",
      "inAppProductId": "9NBLGGH3LHKL",
      "inAppProductName": "Contoso add-on 7",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso Demo",
      "deviceType": "Phone",
      "orderName": "",
      "storeClient": "Windows Phone Store (client)",
      "osVersion": "Windows Phone 8.1",
      "market": "GB",
      "gender": "m",
      "ageGroup": "50orover",
      "acquisitionType": "iap",
      "acquisitionQuantity": 1
    }
  ],
  "@nextLink": "inappacquisitions?applicationId=9NBLGGGZ5QDR&inAppProductId=&aggregationLevel=day&startDate=2015/01/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 33677
}
```

## <a name="related-topics"></a>関連トピック

* [アドオン取得レポート](../publish/add-on-acquisitions-report.md)
* [Microsoft Store サービスを使用して分析データにアクセス](access-analytics-data-using-windows-store-services.md)
* [チャネルによってアドオンの変換を取得します。](get-add-on-conversions-by-channel.md)
* [アプリの取得数を取得します。](get-app-acquisitions.md)
* [アプリの取得のじょうごグラフのデータを取得します。](get-acquisition-funnel-data.md)
* [チャネルによってアプリの変換を取得します。](get-app-conversions-by-channel.md)

 

 
