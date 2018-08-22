---
author: stevewhims
description: HTTP 2.0 プロトコルと HTTP 1.1 プロトコルを使って情報を送受信するには、HttpClient とその他の Windows.Web.Http 名前空間 API を使います。
title: HttpClient
ms.assetid: EC9820D3-3A46-474F-8A01-AE1C27442750
ms.author: stwhi
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: f6b56d9ac156dabc17be3245a77cb6c126317aa9
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "975764"
---
# <a name="httpclient"></a>HttpClient


**重要な API**

-   [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639)
-   [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692)
-   [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631)

HTTP 2.0 プロトコルと HTTP 1.1 プロトコルを使って情報を送受信するには、[**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) とその他の [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) 名前空間 API を使います。

## <a name="overview-of-httpclient-and-the-windowswebhttp-namespace"></a>HttpClient と Windows.Web.Http 名前空間の概要

[**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) 名前空間、関連する [**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) 名前空間、[**Windows.Web.Http.Filters**](https://msdn.microsoft.com/library/windows/apps/dn298623) 名前空間のクラスには、基本的な GET 要求を実行したり、次のようなさらに高度な HTTP 機能を実装したりするための HTTP クライアントとして動作する、ユニバーサル Windows プラットフォーム (UWP) アプリ用のプログラミング インターフェイスが用意されています。

-   一般的な動詞 (**DELETE**、**GET**、**PUT**、**POST**) に対応するメソッド。 これらの各要求は、非同期操作として送られます。

-   一般的な認証設定とパターンのサポート。

-   トランスポートに関する Secure Sockets Layer (SSL) 詳細へのアクセス。

-   カスタマイズされたフィルターを高度なアプリに含める機能。

-   Cookie を取得、設定、削除する機能。

-   非同期メソッドで利用可能な HTTP 要求の進行状況情報。

[**Windows.Web.Http.HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) クラスは、[**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) から送られた HTTP 要求メッセージを表します。 [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) クラスは、HTTP 要求から受け取った HTTP 応答メッセージを表します。 HTTP メッセージは、IETF によって [RFC 2616](http://go.microsoft.com/fwlink/p/?linkid=241642) で規定されています。

[**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) 名前空間は、クッキーを含む HTTP エンティティ ボディおよびヘッダーとして HTTP コンテンツを表します。 HTTP コンテンツは、HTTP 要求または HTTP 応答に関連付けることができます。 **Windows.Web.Http** 名前空間には、HTTP コンテンツを表す多数のさまざまなクラスが用意されています。

-   [**HttpBufferContent**](https://msdn.microsoft.com/library/windows/apps/dn298625)。 バッファーとしてのコンテンツ。
-   [**HttpFormUrlEncodedContent**](https://msdn.microsoft.com/library/windows/apps/dn298685)。 **application/x-www-form-urlencoded** MIME タイプでエンコードされた名前と値の組としてのコンテンツ。
-   [**HttpMultipartContent**](https://msdn.microsoft.com/library/windows/apps/dn298708)。 **multipart/\*** MIME タイプ形式のコンテンツ。
-   [**HttpMultipartFormDataContent**](https://msdn.microsoft.com/library/windows/apps/dn279596)。 **multipart/form-data** MIME タイプとしてエンコードされているコンテンツ。
-   [**HttpStreamContent**](https://msdn.microsoft.com/library/windows/apps/dn279649)。 ストリームとしてのコンテンツ (この内部タイプは、HTTP GET メソッドでのデータの受信、および HTTP POST メソッドでのデータのアップロードに使われます)。
-   [**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661)。 文字列としてのコンテンツ。
-   [**IHttpContent**](https://msdn.microsoft.com/library/windows/apps/dn279684) - 開発者が独自のコンテンツ オブジェクトを作成するための基本インターフェイス

「HTTP 経由でシンプルな GET 要求を送信する」セクションのコード スニペットでは、[**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661) クラスを使って、HTTP GET 要求からの HTTP 応答を文字列として表しています。

[**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) 名前空間では、HTTP ヘッダーと Cookie の作成がサポートされます。これらはプロパティとして、[**HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) オブジェクトと [**HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) オブジェクトに関連付けられます。

## <a name="send-a-simple-get-request-over-http"></a>HTTP 経由でシンプルな GET 要求を送信する

この記事の中で既に説明したように、[**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) 名前空間は、UWP アプリで GET 要求を送信できるようにします。 次のコードが GET 要求を送信する方法を示しますhttp://www.contoso.comを GET 要求から応答メッセージを読む[**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639)クラスと[**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631)クラスを使用します。

```csharp
//Create an HTTP client object
Windows.Web.Http.HttpClient httpClient = new Windows.Web.Http.HttpClient();

//Add a user-agent header to the GET request. 
var headers = httpClient.DefaultRequestHeaders;

//The safe way to add a header value is to use the TryParseAdd method and verify the return value is true,
//especially if the header value is coming from user input.
string header = "ie";
if (!headers.UserAgent.TryParseAdd(header))
{
    throw new Exception("Invalid header value: " + header);
}

header = "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)";
if (!headers.UserAgent.TryParseAdd(header))
{
    throw new Exception("Invalid header value: " + header);
}

Uri requestUri = new Uri("http://www.contoso.com");

//Send the GET request asynchronously and retrieve the response as a string.
Windows.Web.Http.HttpResponseMessage httpResponse = new Windows.Web.Http.HttpResponseMessage();
string httpResponseBody = "";

try
{
    //Send the GET request
    httpResponse = await httpClient.GetAsync(requestUri);
    httpResponse.EnsureSuccessStatusCode();
    httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
}
catch (Exception ex)
{
    httpResponseBody = "Error: " + ex.HResult.ToString("X") + " Message: " + ex.Message;
}
```

## <a name="exceptions-in-windowswebhttp"></a>Windows.Web.Http の例外

Uniform Resource Identifier (URI) として無効な文字列が、[**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) オブジェクトのコンストラクターに渡されると、例外がスローされます。

**.NET:** [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) 型は、C# や VB では [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) と表示されます。

C# や Visual Basic では、.NET 4.5 の [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) クラスと、[**System.Uri.TryCreate**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.trycreate.aspx) メソッドの 1 つを使って、URI が作成される前にユーザーから受け取った文字列をテストすることによって、このエラーを回避できます。

C++ では、URI として渡される文字列を試行して解析するメソッドはありません。 アプリがユーザーから [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) の入力を取得する場合、このコンストラクターを try/catch ブロックに配置する必要があります。 例外がスローされた場合、アプリは、ユーザーに通知し、新しいホスト名を要求することができます。

[**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) には便利な関数がありません。 そのため、この名前空間の [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) と他のクラスを使うアプリは、**HRESULT** 値を使う必要があります。

C# と VB.NET で .NET Framework 4.5 を使うアプリでは、アプリの実行中に例外が発生したときに、[System.Exception](http://msdn.microsoft.com/library/system.exception.aspx) がエラーを表します。 [System.Exception.HResult](http://msdn.microsoft.com/library/system.exception.hresult.aspx) プロパティは、特定の例外に割り当てられた **HRESULT** を返します。 [System.Exception.Message](http://msdn.microsoft.com/library/system.exception.message.aspx) プロパティは、例外を説明するメッセージを返します。 使うことができる **HRESULT** 値は、*Winerror.h* ヘッダー ファイルに記載されています。 アプリは特定の **HRESULT** 値に対するフィルター処理を行い、例外の原因に応じてアプリの動作を変更できます。

Managed C++ を使うアプリでは、アプリの実行中に例外が発生したときに、[Platform::Exception](http://msdn.microsoft.com/library/windows/apps/hh755825.aspx) がエラーを表します。 [Platform::Exception::HResult](http://msdn.microsoft.com/library/windows/apps/hh763371.aspx) プロパティは、特定の例外に割り当てられた **HRESULT** を返します。 [Platform::Exception::Message](http://msdn.microsoft.com/library/windows/apps/hh763375.aspx) プロパティは、**HRESULT** 値に関連付けられた、システムが提供する文字列を返します。 使うことができる **HRESULT** 値は、*Winerror.h* ヘッダー ファイルに記載されています。 アプリは特定の **HRESULT** 値に対するフィルター処理を行い、例外の原因に応じてアプリの動作を変更できます。

ほとんどのパラメーター検証エラーの場合、返される **HRESULT** は **E\_INVALIDARG** です。 一部の無効なメソッド呼び出しでは、返される **HRESULT** は **E\_ILLEGAL\_METHOD\_CALL** です。
