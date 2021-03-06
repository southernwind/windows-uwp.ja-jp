---
ms.assetid: 7B6A99C6-AC86-41A1-85D0-3EB39A7211B6
description: Microsoft Store 送信 API でこのメソッドを使用して、パートナー センター アカウントに登録されているすべてのアプリのすべてのアドオン データを取得します。
title: すべてのアドオンの入手
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, Microsoft Store 申請 API, アドオン, アプリ内製品, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 84b55ea8bc62955e3556fcff4e8d608738eb59ce
ms.sourcegitcommit: 6a7dd4da2fc31ced7d1cdc6f7cf79c2e55dc5833
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58334430"
---
# <a name="get-all-add-ons"></a>すべてのアドオンの入手

Microsoft Store 送信 API でこのメソッドを使用して、データ、パートナー センター アカウントに登録されているすべてのアプリのすべてのアドオンを取得します。

## <a name="prerequisites"></a>前提条件

このメソッドを使うには、最初に次の作業を行う必要があります。

* Microsoft Store 申請 API に関するすべての[前提条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)を満たします (前提条件がまだ満たされていない場合)。
* このメソッドの要求ヘッダーで使う [Azure AD アクセス トークンを取得](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)します。 アクセス トークンを取得した後、アクセス トークンを使用できるのは、その有効期限が切れるまでの 60 分間です。 トークンの有効期限が切れたら新しいトークンを取得できます。

## <a name="request"></a>要求

このメソッドの構文は次のとおりです。 ヘッダーと要求本文の使用例と説明については、次のセクションをご覧ください。

| メソッド | 要求 URI                                                      |
|--------|------------------------------------------------------------------|
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/inappproducts` |


### <a name="request-header"></a>要求ヘッダー

| Header        | 種類   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | 必須。 **Bearer** &lt;*トークン*&gt; という形式の Azure AD アクセス トークン。 |


### <a name="request-parameters"></a>要求パラメーター

このメソッドでは、要求パラメーターはすべてオプションです。 パラメーターを指定せずにこのメソッドを呼び出す場合、応答には、アカウントに登録するすべてのアプリのすべてのアドオンのデータが含まれます。

|  パラメーター  |  種類  |  説明  |  必須  |
|------|------|------|------|
|  top  |  int  |  要求で返される項目の数 (つまり、返されるアドオンの数)。 クエリで指定した値よりアカウントのアドオンの数が多い場合、応答本文には、データの次のページを要求するためにメソッド URI に追加できる相対 URI パスが含まれます。  |  X  |
|  skip  |  int  |  残りの項目を返す前にクエリでバイパスする項目の数。 データ セットを操作するには、このパラメーターを使用します。 たとえば、top = 10 と skip = 0 は、1 から 10 の項目を取得し、top=10 と skip=10 は 11 から 20 の項目を取得するという具合です。  |  いいえ  |


### <a name="request-body"></a>要求本文

このメソッドでは要求本文を指定しないでください。

### <a name="request-examples"></a>要求の例

次の例は、アカウントに登録するすべてのアプリのすべてのアドオン データを取得する方法を示しています。

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/inappproducts HTTP/1.1
Authorization: Bearer <your access token>
```

次の例は、最初の 10 個のアドオンのみを取得する方法を示しています。

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/inappproducts?top=10 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>応答

次の例は、合計 1072 個のアドオンがある開発者アカウントに登録されている、最初の 5 個のアドオンに対する要求が成功した場合に返される JSON 応答本文を示しています。 簡潔にするために、この例では、要求によって返される最初の 2 つのアドオンのデータのみが示されています。 応答本文の値について詳しくは、次のセクションをご覧ください。

```json
{
  "@nextLink": "inappproducts/?skip=5&top=5",
  "value": [
    {
      "applications": {
        "value": [
          {
            "id": "9NBLGGH4R315",
            "resourceLocation": "applications/9NBLGGH4R315"
          }
        ],
        "totalCount": 1
      },
      "id": "9NBLGGH4TNMP",
      "productId": "a8b8310b-fa8d-4da0-aca0-577ef6dce4dd",
      "productType": "Consumable",
      "pendingInAppProductSubmission": {
        "id": "1152921504621243619",
        "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243619"
      },
      "lastPublishedInAppProductSubmission": {
        "id": "1152921504621243705",
        "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243705"
      }
    },
    {
      "applications": {
        "value": [
          {
            "id": "9NBLGGH4R315",
            "resourceLocation": "applications/9NBLGGH4R315"
          }
        ],
        "totalCount": 1
      },
      "id": "9NBLGGH4TNMN",
      "productId": "6a3c9788-a350-448a-bd32-16160a13018a",
      "productType": "Consumable",
      "pendingInAppProductSubmission": {
        "id": "1152921504621243538",
        "resourceLocation": "inappproducts/9NBLGGH4TNMN/submissions/1152921504621243538"
      },
      "lastPublishedInAppProductSubmission": {
        "id": "1152921504621243106",
        "resourceLocation": "inappproducts/9NBLGGH4TNMN/submissions/1152921504621243106"
      }
    },

  // Other add-ons omitted for brevity...
  ],
  "totalCount": 1072
}
```

### <a name="response-body"></a>応答本文

| Value      | 種類   | 説明                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| @nextLink  | string | データの追加ページが存在する場合、この文字列には、データの次のページを要求するために、ベースとなる `https://manage.devcenter.microsoft.com/v1.0/my/` 要求 URI に追加できる相対パスが含まれます。 たとえば、最初の要求本文の *top* パラメーターが 10 に設定されていて、アカウントには 100 個のアドオンが登録されている場合、応答本文には、`inappproducts?skip=10&top=10` という @nextLink 値が含まれます。これは、次の 10 個のアドオンを要求するために、`https://manage.devcenter.microsoft.com/v1.0/my/inappproducts?skip=10&top=10` を呼び出すことができることを示しています。 |
| value            | array  |  各アドオンに関する情報を提供するオブジェクトを格納する配列。 詳しくは、「[アドオン リソース](manage-add-ons.md#add-on-object)」をご覧ください。   |
| totalCount   | int  | 応答本文の *value* 配列のアプリ オブジェクトの数。     |


## <a name="error-codes"></a>エラー コード

要求を正常に完了できない場合、次の HTTP エラー コードのいずれかが応答に含まれます。

| エラー コード |  説明   |
|--------|------------------|
| 404  | アドオンは見つかりませんでした。 |
| 409  | アプリまたはアドオンはパートナー センターの機能を使用して、[現在サポートされていません、Microsoft Store 送信 API](create-and-manage-submissions-using-windows-store-services.md#not_supported)します。  |


## <a name="related-topics"></a>関連トピック

* [作成し、Microsoft Store サービスを使用して送信の管理](create-and-manage-submissions-using-windows-store-services.md)
* [アドオンの送信を管理します。](manage-add-on-submissions.md)
* [アドオンを入手します。](get-an-add-on.md)
* [アドオンを作成します。](create-an-add-on.md)
* [アドオンを削除します。](delete-an-add-on.md)
