---
description: データを取得する詳細な特定のエラー、Xbox One のゲームを Microsoft Store 分析 API でこのメソッドを使います。
title: ゲームの Xbox One のエラーに関する詳細を取得します。
ms.date: 11/06/2018
ms.topic: article
keywords: Windows 10, UWP, Store サービス, Microsoft Store 分析 API, エラー, 詳細
ms.localizationpriority: medium
ms.openlocfilehash: 6b713e3c6c2f7b82e5779e4785cc6b2e320b24f0
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2018
ms.locfileid: "7712606"
---
# <a name="get-details-for-an-error-in-your-xbox-one-game"></a>ゲームの Xbox One のエラーに関する詳細を取得します。

Microsoft Store 分析 API に関する詳細データを取得する特定のエラー、Xbox One のゲームを Xbox デベロッパー ポータル (XDP) を通じて取り込まれ、XDP 分析のパートナー センター ダッシュ ボードで利用するには、このメソッドを使います。 このメソッドで取得できるのは、過去 30 日以内に発生したエラーの詳細のみです。

以下のメソッドを使用する前に、まずは、詳細情報を取得するエラーの ID を取得するのにことで、 [Xbox One ゲームに関するエラー報告データを取得する](get-error-reporting-data-for-your-xbox-one-game.md)メソッドを使用する必要があります。

## <a name="prerequisites"></a>前提条件


このメソッドを使うには、最初に次の作業を行う必要があります。

* Microsoft Store 分析 API に関するすべての[前提条件](access-analytics-data-using-windows-store-services.md#prerequisites)を満たします (前提条件がまだ満たされていない場合)。
* このメソッドの要求ヘッダーで使う [Azure AD アクセス トークンを取得](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token)します。 アクセス トークンを取得した後、アクセス トークンを使用できるのは、その有効期限が切れるまでの 60 分間です。 トークンの有効期限が切れたら、新しいトークンを取得できます。
* 詳細情報を取得するエラーの ID を取得します。 この ID を取得するには、[ゲーム、Xbox One のエラー報告データを取得する](get-error-reporting-data-for-your-xbox-one-game.md)メソッドを使用して、そのメソッドの応答本文で**failureHash**値を使用します。

## <a name="request"></a>要求


### <a name="request-syntax"></a>要求の構文

| メソッド | 要求 URI                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failuredetails``` |


### <a name="request-header"></a>要求ヘッダー

| ヘッダー        | 型   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | 必須。 **Bearer** &lt;*トークン*&gt; という形式の Azure AD アクセス トークン。 |


### <a name="request-parameters"></a>要求パラメーター

| パラメーター        | 型   |  説明      |  必須かどうか  
|---------------|--------|---------------|------|
| applicationId | string | エラーの詳細を取得する Xbox One ゲームの製品 ID です。 ゲームの製品 ID を取得するには、Xbox デベロッパー ポータル (XDP) で目的のゲームに移動し、URL から製品 ID を取得します。 または、Windows パートナー センターの分析レポートから正常性データをダウンロードした場合、製品 ID は .tsv ファイルに含まれています。 |  必須  |
| failureHash | string | 取得する詳細情報の対象となるエラーの一意の ID です。 興味のあるエラーにこの値を取得するには、[ゲーム、Xbox One のエラー報告データを取得する](get-error-reporting-data-for-your-xbox-one-game.md)メソッドを使用し、そのメソッドの応答本文で**failureHash**値を使用します。 |  必須  |
| startDate | date | 取得する詳細なエラー データの日付範囲の開始日です。 既定値は、現在の日付の 30 日前です。 |  必須ではない  |
| endDate | date | 取得する詳細なエラー データの日付範囲の終了日です。 既定値は現在の日付です。 |  必須ではない  |
| top | int | 要求で返すデータの行数です。 指定されない場合の既定値は、最大値でもある 10000 です。 クエリにこれを上回る行がある場合は、応答本文に次リンクが含まれ、そのリンクを使ってデータの次のページを要求できます。 |  必須ではない  |
| skip | int | クエリでスキップする行数です。 大きなデータ セットを操作するには、このパラメーターを使用します。 たとえば、top=10 と skip=0 を指定すると、データの最初の 10 行が取得され、top=10 と skip=10 を指定すると、データの次の 10 行が取得されます。 |  必須ではない  |
| filter |string  | 応答内の行をフィルター処理する 1 つまたは複数のステートメントです。 各ステートメントには、応答本文からのフィールド名、および **eq** 演算子または **ne** 演算子と関連付けられる値が含まれており、**and** や **or** を使用してステートメントを組み合わせることができます。 *filter* パラメーターでは、文字列値を単一引用符で囲む必要があります。 応答本文から次のフィールドを指定することができます。<p/><ul><li><strong>market</strong></li><li><strong>date</strong></li><li><strong>cabId</strong></li><li><strong>cabExpirationTime</strong></li><li><strong>deviceType</strong></li><li><strong>deviceModel</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li><li><strong>packageVersion</strong></li><li><strong>osBuild</strong></li></ul> | 必須ではない   |
| orderby | string | 結果データ値の順序を指定するステートメントです。 構文は <em>orderby=field [order],field [order],...</em> です。<em>field</em> パラメーターは次のいずれかの文字列になります。<ul><li><strong>market</strong></li><li><strong>date</strong></li><li><strong>cabId</strong></li><li><strong>cabExpirationTime</strong></li><li><strong>deviceType</strong></li><li><strong>deviceModel</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li><li><strong>packageVersion</strong></li><li><strong>osBuild</strong></li></ul><p><em>order</em> パラメーターは省略可能であり、<strong>asc</strong> または <strong>desc</strong> を指定して、各フィールドを昇順または降順にすることができます。 既定値は <strong>asc</strong> です。</p><p><em>orderby</em> 文字列の例: <em>orderby=date,market</em></p> |  必須ではない  |


### <a name="request-example"></a>要求の例

次の例では、ゲームの Xbox One の詳細なエラー データを取得するためのいくつかの要求を示します。 *ApplicationId*値をゲームの製品 ID に置き換えます。

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failuredetails?applicationId=BRRT4NJ9B3D1&failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failuredetails?applicationId=BRRT4NJ9B3D1&failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0&filter=market eq 'US' and deviceType eq 'Windows.Desktop' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>応答


### <a name="response-body"></a>応答本文

| 値      | 型    | 説明    |
|------------|---------|------------|
| Value      | array   | 詳細なエラー データが含まれているオブジェクトの配列です。 各オブジェクトのデータについて詳しくは、次の「[エラーの詳細情報の値](#error-detail-values)」セクションをご覧ください。          |
| @nextLink  | string  | データの追加ページがある場合、この文字列には、データの次のページを要求するために使用できる URI が含まれます。 たとえば、要求の **top** パラメーターを 10 に設定した場合、クエリに適合するエラーが 10 行を超えると、この値が返されます。 |
| TotalCount | 整数 | クエリの結果データ内の行の総数です。        |


<span id="error-detail-values"/>

### <a name="error-detail-values"></a>エラーの詳細情報の値

*Value* 配列の要素には、次の値が含まれます。

| 値           | 型    | 説明     |
|-----------------|---------|----------------------------|
| applicationId   | string  | 詳細なエラー データを取得した Xbox One ゲームの製品 ID です。      |
| failureHash     | string  | エラーの一意の識別子です。     |
| failureName     | string  | 4 つの部分から成るエラーの名前です。問題が発生した 1 つ以上のクラス、例外/バグ チェック コード、障害が発生したイメージの名前、関連する関数の名前で構成されます。           |
| date            | string  | エラー データの日付範囲の最初の日付です。 要求に日付を指定した場合、この値はその日付になります。 要求に週、月、またはその他の日付範囲を指定した場合、この値はその日付範囲の最初の日付になります。 |
| cabId           | string  | このエラーに関連付けられている CAB ファイルの一意の ID です。   |
| cabExpirationTime  | string  | CAB ファイルの有効期限が切れ、ダウンロードできなくなる日付と時刻 (ISO 8601 形式) です。   |
| market          | string  | デバイスの市場の ISO 3166 国コードです。     |
| osBuild         | string  | エラーが発生した OS のビルド番号です。       |
| packageVersion  | string  | このエラーに関連付けられているゲームのパッケージのバージョンです。    |
| deviceModel           | string  | エラーが発生したときに、ゲームが実行されていた Xbox One 本体を指定する次の文字列のいずれか。<p/><ul><li><strong>Microsoft Xbox One</strong></li><li><strong>Microsoft Xbox One S</strong></li><li><strong>Microsoft Xbox One X</strong></li></ul>  |
| osVersion       | string  | エラーが発生した OS のバージョンです。 これは、常に、 **Windows 10**の値です。    |
| osRelease       | string  |  エラーが発生したを (OS バージョン内のサブグループ) としての Windows 10 OS リリースまたはフライティング リングを指定する次の文字列のいずれか。<p/><ul><li><strong>Version 1507</strong></li><li><strong>Version 1511</strong></li><li><strong>Version 1607</strong></li><li><strong>Version 1703</strong></li><li><strong>Version 1709</strong></li><li><strong>バージョン 1803</strong></li><li><strong>リリース プレビュー</strong></li><li><strong>Insider Fast</strong></li><li><strong>Insider Slow</strong></li></ul><p>OS リリースまたはフライティング リングが不明な場合、このフィールドは値 <strong>Unknown</strong> になります。</p>    |
| deviceType      | string  | エラーが発生したデバイスの種類です。 これは、常に、**コンソール**の値です。     |
| cabDownloadable           | Boolean  | このユーザーが CAB ファイルをダウンロードできるかどうかを示します。   |


### <a name="response-example"></a>応答の例

この要求の JSON 応答の本文の例を次に示します。

```json
{
  "Value": [
    {
      "applicationId": "BRRT4NJ9B3D1",
      "failureHash": "012345-5dbc9-b12f-c124-9d9810f05d8b",
      "failureName": "STOWED_EXCEPTION_System.UriFormatException_exe!ContosoSports.GroupedItems+_ItemView_ItemClick_d__9.MoveNext",
      "date": "2018-02-05 09:11:25",
      "cabId": "133637331323",
      "cabExpirationTime": "2016-12-05 09:11:25",
      "market": "US",
      "osBuild": "10.0.17134",
      "packageVersion": "1.0.2.6",
      "deviceModel": "Microsoft-Xbox One",
      "osVersion": "Windows 10",
      "osRelease": "Version 1803",
      "deviceType": "Console",
      "cabDownloadable": false
    }
  ],
  "@nextLink": null,
  "TotalCount": 1
}
```

## <a name="related-topics"></a>関連トピック

* [Microsoft Store サービスを使った分析データへのアクセス](access-analytics-data-using-windows-store-services.md)
* [ゲームの Xbox One に関するエラー報告データを取得します。](get-error-reporting-data-for-your-xbox-one-game.md)
* [ゲームに Xbox One でのエラーに関するスタック トレースを取得します。](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)
* [Xbox One ゲームのエラーに関する CAB ファイルをダウンロードします。](download-the-cab-file-for-an-error-in-your-xbox-one-game.md)