---
ms.assetid: 24C5F796-5FB8-4B5D-B428-C3154B3098BD
description: 既存のパッケージ フライトの申請を更新するには、Microsoft Store 申請 API の以下のメソッドを使います。
title: パッケージ フライトの申請の更新
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, Microsoft Store 申請 API, フライトの申請, 更新
ms.localizationpriority: medium
ms.openlocfilehash: a06f341584c88be06e4f8c23a3b86bec9d1cec28
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360901"
---
# <a name="update-a-package-flight-submission"></a>パッケージ フライトの申請の更新


既存のパッケージ フライトの申請を更新するには、Microsoft Store 申請 API の以下のメソッドを使います。 このメソッドを使って申請を正常に更新した後は、インジェストと公開のために[申請をコミット](commit-a-flight-submission.md)する必要があります。

このメソッドが Microsoft Store 申請 API を使ったパッケージ フライト申請の作成プロセスにどのように適合するかについては、「[パッケージ フライトの申請の管理](manage-flight-submissions.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このメソッドを使うには、最初に次の作業を行う必要があります。

* Microsoft Store 申請 API に関するすべての[前提条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)を満たします (前提条件がまだ満たされていない場合)。
* このメソッドの要求ヘッダーで使う [Azure AD アクセス トークンを取得](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)します。 アクセス トークンを取得した後、アクセス トークンを使用できるのは、その有効期限が切れるまでの 60 分間です。 トークンの有効期限が切れたら新しいトークンを取得できます。
* アプリのいずれかのパッケージのフライトの送信を作成します。 パートナー センターでこれを行うかを使用してこれを行う、[パッケージ フライトの提出の作成](create-a-flight-submission.md)メソッド。

## <a name="request"></a>要求

このメソッドの構文は次のとおりです。 ヘッダーと要求本文の使用例と説明については、次のセクションをご覧ください。

| メソッド | 要求 URI                                                      |
|--------|------------------------------------------------------------------|
| PUT    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}``` |


### <a name="request-header"></a>要求ヘッダー

| Header        | 種類   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | 必須。 **Bearer** &lt;*トークン*&gt; という形式の Azure AD アクセス トークン。 |


### <a name="request-parameters"></a>要求パラメーター

| 名前        | 種類   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | 必須。 パッケージ フライト申請を更新するアプリのストア ID です。 ストア ID について詳しくは、「[アプリ ID の詳細の表示](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details)」をご覧ください。  |
| flightId | string | 必須。 申請を更新するパッケージ フライトの ID です。 この ID は、[パッケージ フライトの作成](create-a-flight.md)要求と[アプリのパッケージ フライトの取得](get-flights-for-an-app.md)要求の応答データで確認できます。 パートナー センターで作成されたフライトはこの ID はパートナー センターでのフライトのページの URL で使用できるも。  |
| submissionId | string | 必須。 更新する申請の ID です。 この ID は、[パッケージ フライトの申請の作成](create-a-flight-submission.md)要求に対する応答データで確認できます。 パートナー センターで作成された送信、この ID はパートナー センターでの送信 ページの URL で使用できるも。  |


### <a name="request-body"></a>要求本文

要求本文には次のパラメーターがあります。

| Value      | 種類   | 説明                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| flightPackages           | array  | 申請の各パッケージに関する詳細を提供するオブジェクトが含まれています。 応答本文の値について詳しくは、「[フライト パッケージ リソース](manage-flight-submissions.md#flight-package-object)」をご覧ください。 このメソッドを呼び出してアプリの申請を更新するとき、要求の本文では、これらのオブジェクトの値 *fileName*、*fileStatus*、*minimumDirectXVersion*、*minimumSystemRam* だけが必須です。 パートナー センターでは、その他の値が設定されます。 |
| packageDeliveryOptions    | オブジェクト  | 申請の段階的なパッケージのロールアウトと必須の更新の設定が含まれています。 詳しくは、「[パッケージの配信オプション オブジェクト](manage-flight-submissions.md#package-delivery-options-object)」をご覧ください。  |
| targetPublishMode           | string  | 申請の公開モードです。 次のいずれかの値を使用できます。 <ul><li>即時</li><li>Manual</li><li>SpecificDate</li></ul> |
| targetPublishDate           | string  | *targetPublishMode* が SpecificDate に設定されている場合、ISO 8601 形式での申請の公開日です。  |
| notesForCertification           | string  |  テスト アカウントの資格情報や、機能のアクセスおよび検証手順など、審査担当者に対して追加情報を提供します。 詳しくは、「[認定の注意書き](https://docs.microsoft.com/windows/uwp/publish/notes-for-certification)」をご覧ください。 |


### <a name="request-example"></a>要求の例

アプリのパッケージ フライト申請を更新する方法の例を次に示します。

```json
PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243649 HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
{
  "flightPackages": [
    {
      "fileName": "newPackage.appx",
      "fileStatus": "PendingUpload",
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "targetPublishMode": "Immediate",
  "targetPublishDate": "",
  "notesForCertification": "No special steps are required for certification of this app."
}
```

## <a name="response"></a>応答

次の例は、このメソッドが正常に呼び出された場合の JSON 応答本文を示しています。 応答本文には、更新された申請に関する情報が含まれています。 応答本文の値について詳しくは、「[パッケージ フライトの申請のリソース](manage-flight-submissions.md#flight-submission-object)」を参照してください。

```json
{
  "id": "1152921504621243649",
  "flightId": "cd2e368a-0da5-4026-9f34-0e7934bc6f23",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [],
    "warnings": [],
    "certificationReports": []
  },
  "flightPackages": [
    {
      "fileName": "newPackage.appx",
      "fileStatus": "PendingUpload",
      "id": "",
      "version": "1.0.0.0",
      "languages": ["en-us"],
      "capabilities": [],
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/8b389577-5d5e-4cbe-a744-1ff2e97a9eb8?sv=2014-02-14&sr=b&sig=wgMCQPjPDkuuxNLkeG35rfHaMToebCxBNMPw7WABdXU%3D&se=2016-06-17T21:29:44Z&sp=rwl",
  "targetPublishMode": "Immediate",
  "targetPublishDate": "",
  "notesForCertification": "No special steps are required for certification of this app."
}
```

## <a name="error-codes"></a>エラー コード

要求を正常に完了できない場合、次の HTTP エラー コードのいずれかが応答に含まれます。

| エラー コード |  説明   |
|--------|------------------|
| 400  | 要求が正しくないため、パッケージ フライト申請を更新できませんでした。 |
| 409  | パッケージのフライトの送信をアプリの現在の状態であるため更新できませんでしたまたはアプリであるパートナー センター機能を使用する[現在サポートされていません、Microsoft Store 送信 API](create-and-manage-submissions-using-windows-store-services.md#not_supported)。 |   


## <a name="related-topics"></a>関連トピック

* [作成し、Microsoft Store サービスを使用して送信の管理](create-and-manage-submissions-using-windows-store-services.md)
* [パッケージのフライトの送信を管理します。](manage-flight-submissions.md)
* [パッケージのフライトの送信を取得します。](get-a-flight-submission.md)
* [パッケージのフライトの提出を作成します。](create-a-flight-submission.md)
* [パッケージのフライトの送信をコミットします。](commit-a-flight-submission.md)
* [パッケージのフライトの送信を削除します。](delete-a-flight-submission.md)
* [パッケージのフライトの送信の状態を取得します。](get-status-for-a-flight-submission.md)
