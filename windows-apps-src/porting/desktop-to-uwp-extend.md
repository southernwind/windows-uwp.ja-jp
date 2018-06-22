---
author: normesta
Description: Extend your desktop application with Windows UIs and components
Search.Product: eADQiWindows 10XVcnh
title: Windows UI とコンポーネントによるデスクトップ アプリケーションの拡張
ms.author: normesta
ms.date: 03/22/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: ef20366092a5f284c39f4e43d4412c69b60f12fa
ms.sourcegitcommit: 6618517dc0a4e4100af06e6d27fac133d317e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
ms.locfileid: "1691331"
---
# <a name="extend-your-desktop-application-with-modern-uwp-components"></a>最新の UWP コンポーネントによるデスクトップ アプリケーションの拡張

一部の Windows 10 エクスペリエンス (タッチ対応 UI ページなど) は、最新のアプリ コンテナー内で実行する必要があります。 こうしたエクスペリエンスを追加するには、UWP プロジェクトと Windows ランタイム コンポーネントを使ってデスクトップ アプリケーションを拡張します。

多くの場合、デスクトップ アプリケーションから UWP API を直接呼び出すことができます。そのため、このガイドを確認する前に、「[Windows 10 のための拡張](desktop-to-uwp-enhance.md)」をご覧ください。

>[!NOTE]
>このガイドは、デスクトップ ブリッジを使用してデスクトップ アプリケーションの Windows アプリ パッケージを作成済みであることを前提としています。 この作業をまだ行っていない場合は、「[デスクトップ ブリッジ](desktop-to-uwp-root.md)」をご覧ください。

準備ができたら始めましょう。

## <a name="first-setup-your-solution"></a>まず、ソリューションをセットアップする

UWP プロジェクトとランタイム コンポーネントを 1 つ以上ソリューションに追加します。

**Windows アプリケーション パッケージ プロジェクト**とデスクトップ アプリケーションへの参照が含まれるソリューションから始めます。

次の画像は、ソリューションの例を示しています。

![開始プロジェクトを拡張する](images/desktop-to-uwp/extend-start-project.png)

ソリューションにパッケージ プロジェクトが含まれていない場合、[Visual Studio を使ったアプリのパッケージ化に関するページ](desktop-to-uwp-packaging-dot-net.md)をご覧ください。

### <a name="add-a-uwp-project"></a>UWP プロジェクトを追加する

ソリューションに **[空白のアプリ (ユニバーサル Windows)]** を追加します。

ここでは、最新の XAML UI をビルドするか、UWP プロセス内でのみ実行される API を使います。

![UWP プロジェクト](images/desktop-to-uwp/add-uwp-project-to-solution.png)

パッケージ プロジェクトで、**[アプリケーション]** ノードを右クリックして **[参照の追加]** をクリックします。

![UWP プロジェクトを参照する](images/desktop-to-uwp/add-uwp-project-reference.png)

次に、UWP プロジェクトに参照を追加します。

![UWP プロジェクトを参照する](images/desktop-to-uwp/choose-uwp-project.png)

ソリューションは次のようになります。

![UWP プロジェクトのあるソリューション](images/desktop-to-uwp/uwp-project-reference.png)

### <a name="optional-add-a-windows-runtime-component"></a>(省略可能) Windows ランタイム コンポーネントを追加する

いくつかのシナリオでは、Windows ランタイム コンポーネントにコードを追加する必要があります。

![ランタイム コンポーネントのアプリ サービス](images/desktop-to-uwp/add-runtime-component.png)

次に、UWP プロジェクトからランタイム コンポーネントに参照を追加します。 ソリューションは次のようになります。

![ランタイム コンポーネント参照](images/desktop-to-uwp/runtime-component-reference.png)

UWP プロジェクトとランタイム コンポーネントで行うことができる操作をいくつか見てみましょう。

## <a name="show-a-modern-xaml-ui"></a>最新の XAML UI を表示する

アプリケーション フローの一環として、最新の XAML ベースのユーザー インターフェイスをデスクトップ アプリケーションに組み込むことができます。 これらのユーザー インターフェイスは、さまざまな画面サイズと解像度に適応し、タッチや手描きなどの最新の対話モデルをサポートする性質を備えています。

たとえば、少量の XAML マークアップを使用して、地図関連の強力な視覚化機能をユーザーに提供できます。

次の画像に、マップ コントロールを含む XAML ベースの最新の UI を表示している VB6 アプリケーションを示しています。

![アダプティブ デザイン](images/desktop-to-uwp/extend-xaml-ui.png)

### <a name="have-a-closer-look-at-this-app"></a>このアプリを詳しく確認する

:heavy_check_mark: [アプリを入手する](https://www.microsoft.com/en-us/store/p/vb6-app-with-xaml-sample/9n191ncxf2f6)

:heavy_check_mark: [コードを参照する](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/VB6withXaml)

### <a name="the-design-pattern"></a>設計パターン

XAML ベースの UI を表示するには、以下の手順を実行します。

:1: [そのプロジェクトにプロトコル拡張機能を追加する](#protocol)

:2: [デスクトップ アプリから UWP アプリを起動する](#start)

:3: [UWP プロジェクトで目的のページを表示する](#parse)

<a id="protocol" />

### <a name="add-a-protocol-extension"></a>プロトコル拡張機能を追加する

**ソリューション エクスプ ローラー**で、UWP プロジェクトの **package.appxmanifest** ファイルを開き、この拡張機能を追加します。

```xml
<Extensions>
      <uap:Extension
          Category="windows.protocol"
          Executable="MapUI.exe"
          EntryPoint=" MapUI.App">
        <uap:Protocol Name="desktopbridgemapsample" />
      </uap:Extension>
    </Extensions>     
```

プロトコルに名前を付けて、UWP プロジェクトによって生成された実行可能ファイルの名前と、エントリ ポイント クラスの名前を指定します。

デザイナーで **package.appxmanifest** 開き、**[宣言]** タブを選んで、そこで拡張機能を追加することもできます。

![[宣言] タブ](images/desktop-to-uwp/protocol-properties.png)



> [!NOTE]
> マップ コントロールはインターネットからデータをダウンロードします。そのため、マップ コントロールを使用する場合は、"インターネット クライアント" 機能もマニフェストに追加する必要があります。

<a id="start" />

### <a name="start-the-uwp-app"></a>UWP アプリを起動する

まず、デスクトップ アプリケーションから、プロトコル名と UWP アプリに渡すパラメーターが含まれた [URI](https://msdn.microsoft.com/library/system.uri.aspx) を作成します。 次に、[LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) メソッドを呼び出します。

C# の基本的な例を次に示します。

```csharp

private async void showMap(double lat, double lon)
{
    string str = "desktopbridgemapsample://";

    Uri uri = new Uri(str + "location?lat=" +
        lat.ToString() + "&?lon=" + lon.ToString());

    var success = await Windows.System.Launcher.LaunchUriAsync(uri);

    if (success)
    {
        // URI launched
    }
    else
    {
        // URI launch failed
    }
}
```
このサンプルでは、もう少し間接的な作業を行います。 ``LaunchMap`` という名前の VB6 で呼び出せる相互運用関数に呼び出しをラップしました。 この関数は C++ を使用して記述されています。

VB ブロックを次に示します。

```VB
Private Declare Function LaunchMap Lib "UWPWrappers.dll" _
  (ByVal lat As Double, ByVal lon As Double) As Boolean
 
Private Sub EiffelTower_Click()
    LaunchMap 48.858222, 2.2945
End Sub
```

C++ 関数を次に示します。

```C++

DllExport bool __stdcall LaunchMap(double lat, double lon)
{
  try
  {
    String ^str = ref new String(L"desktopbridgemapsample://");
    Uri ^uri = ref new Uri(
      str + L"location?lat=" + lat.ToString() + L"&?lon=" + lon.ToString());
 
    // now launch the UWP component
    Launcher::LaunchUriAsync(uri);
  }
  catch (Exception^ ex) { return false; }
  return true;
}

```

<a id="parse" />

### <a name="parse-parameters-and-show-a-page"></a>パラメーターを解析してページを表示する

UWP プロジェクトの **App** クラスで、**OnActivated** イベント ハンドラーをオーバーライドします。 アプリがプロトコルによってアクティブ化されている場合は、パラメーターを解析して目的のページを開きます。

```C++
void App::OnActivated(Windows::ApplicationModel::Activation::IActivatedEventArgs^ e)
{
  if (e->Kind == ActivationKind::Protocol)
  {
    ProtocolActivatedEventArgs^ protocolArgs = (ProtocolActivatedEventArgs^)e;
    Uri ^uri = protocolArgs->Uri;
    if (uri->SchemeName == "desktopbridgemapsample")
    {
      Frame ^rootFrame = ref new Frame();
      Window::Current->Content = rootFrame;
      rootFrame->Navigate(TypeName(MainPage::typeid), uri->Query);
      Window::Current->Activate();
    }
  }
}
```

### <a name="similar-samples"></a>類似のサンプル

[Northwind サンプル: UWA UI および Win32 レガシ コードのエンド ツー エンドの例](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/NorthwindSample)

[Northwind サンプル: SQL Server に接続する UWP アプリ](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SQLServer)

## <a name="provide-services-to-other-apps"></a>サービスを他のアプリに提供する

他のアプリで利用できるサービスを追加します。 たとえば、アプリの背後でデータベースが実行されている場合に、そのデータベースへの制御されたアクセスを他のアプリに提供するサービスを追加できます。 バックグラウンド タスクを実装することで、デスクトップ アプリが実行されていない場合でも他のアプリからサービスにアクセスできます。

これを実行するサンプルを次に示します。

![アダプティブ デザイン](images/desktop-to-uwp/winforms-app-service.png)

### <a name="have-a-closer-look-at-this-app"></a>このアプリを詳しく確認する

:heavy_check_mark: [アプリを入手する](https://www.microsoft.com/en-us/store/p/winforms-appservice/9p7d9b6nk5tn)

:heavy_check_mark: [コードを参照する](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WinformsAppService)

### <a name="the-design-pattern"></a>設計パターン

サービスの提供を示すには、以下の手順を実行します。

:1: [アプリ サービスを実装する](#appservice)

:2: [アプリ サービスの拡張機能を追加する](#extension)

:3: [アプリ サービスをテストする](#test)

<a id="appservice" />

### <a name="implement-the-app-service"></a>アプリ サービスを実装する

以下では、他のアプリからの要求を検証して処理します。 ソリューションで Windows ランタイム コンポーネントにこのコードを追加します。

```csharp
public sealed class AppServiceTask : IBackgroundTask
{
    private BackgroundTaskDeferral backgroundTaskDeferral;
 
    public void Run(IBackgroundTaskInstance taskInstance)
    {
        this.backgroundTaskDeferral = taskInstance.GetDeferral();
        taskInstance.Canceled += OnTaskCanceled;
        var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
        details.AppServiceConnection.RequestReceived += OnRequestReceived;
    }
 
    private async void OnRequestReceived(AppServiceConnection sender,
                                         AppServiceRequestReceivedEventArgs args)
    {
        var messageDeferral = args.GetDeferral();
        ValueSet message = args.Request.Message;
        string id = message["ID"] as string;
        ValueSet returnData = DataBase.GetData(id);
        await args.Request.SendResponseAsync(returnData);
        messageDeferral.Complete();
    }
 
 
    private void OnTaskCanceled(IBackgroundTaskInstance sender,
                                BackgroundTaskCancellationReason reason)
    {
        if (this.backgroundTaskDeferral != null)
        {
            this.backgroundTaskDeferral.Complete();
        }
    }
}
```

<a id="extension" />

### <a name="add-an-app-service-extension-to-the-uwp-project"></a>UWP プロジェクトにアプリ サービスの拡張機能を追加する

UWP プロジェクトの **package.appxmanifest** ファイルを開き、アプリ サービスの拡張機能を ``<Application>`` 要素に追加します。

```xml
<Extensions>
      <uap:Extension
          Category="windows.appService"
          EntryPoint="AppServiceComponent.AppServiceTask">
        <uap:AppService Name="com.microsoft.samples.winforms" />
      </uap:Extension>
    </Extensions>    
```
そのアプリ サービスに名前を付けて、エントリ ポイント クラスの名前を指定します。 これは、サービスを実装したクラスです。

<a id="test" />

### <a name="test-the-app-service"></a>アプリ サービスをテストする

別のアプリからサービスを呼び出すことにより、サービスをテストします。 このコードは、Windows フォーム アプリや別の UWP アプリなどのデスクトップ アプリケーションにすることができます。

> [!NOTE]
> このコードは、``AppServiceConnection`` クラスの ``PackageFamilyName`` プロパティを適切に設定した場合のみ動作します。 その名前を取得するには、UWP プロジェクトで ``Windows.ApplicationModel.Package.Current.Id.FamilyName`` を呼び出します。 「[App Service の作成と利用](https://docs.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)」をご覧ください。

```csharp
private async void button_Click(object sender, RoutedEventArgs e)
{
    AppServiceConnection dataService = new AppServiceConnection();
    dataService.AppServiceName = "com.microsoft.samples.winforms";
    dataService.PackageFamilyName = "Microsoft.SDKSamples.WinformWithAppService";
 
    var status = await dataService.OpenAsync();
    if (status == AppServiceConnectionStatus.Success)
    {
        string id = int.Parse(textBox.Text);
        var message = new ValueSet();
        message.Add("ID", id);
        AppServiceResponse response = await dataService.SendMessageAsync(message);
 
        if (response.Status == AppServiceResponseStatus.Success)
        {
            if (response.Message["Status"] as string == "OK")
            {
                DisplayResult(response.Message["Result"]);
            }
        }
    }
}
```

アプリ サービスについて詳しくは、「[アプリ サービスの作成と利用](https://docs.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)」をご覧ください。

### <a name="similar-samples"></a>類似のサンプル

[アプリ サービス ブリッジのサンプル](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/AppServiceBridgeSample)

[C++ Win32 アプリを使ったアプリ サービス ブリッジのサンプル](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/AppServiceBridgeSample_C%2B%2B)

[プッシュ通知を受け取る MFC アプリケーション](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/MFCwithPush)


## <a name="making-your-desktop-application-a-share-target"></a>デスクトップ アプリケーションを共有ターゲットにする

デスクトップ アプリケーションを共有ターゲットにすることで、共有をサポートしている他のアプリのデータ (画像など) をユーザーが簡単に共有できるようになります。

たとえば、ユーザーは、Microsoft Edge やフォト アプリから画像を共有するためにアプリを選択できます。 そのような機能を備えた WPF サンプル アプリは次のとおりです。

![共有ターゲット](images/desktop-to-uwp/share-target.png)

### <a name="have-a-closer-look-at-this-app"></a>このアプリを詳しく確認する

:heavy_check_mark: [アプリを入手する](https://www.microsoft.com/en-us/store/p/wpf-app-as-sharetarget/9pjcjljlck37)

:heavy_check_mark: [コードを参照する](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WPFasShareTarget)

### <a name="the-design-pattern"></a>設計パターン

アプリケーションを共有ターゲットにするには、以下の手順を実行します。

:1: [共有ターゲットの拡張機能を追加する](#share-extension)

:2: [OnNavigatedTo イベント ハンドラーをオーバーライドする](#override)

<a id="share-extension" />

### <a name="add-a-share-target-extension"></a>共有ターゲットの拡張機能を追加する

**ソリューション エクスプ ローラー**で、UWP プロジェクトの **package.appxmanifest** ファイルを開き、拡張機能を追加します。

```xml
<Extensions>
      <uap:Extension
          Category="windows.shareTarget"
          Executable="ShareTarget.exe"
          EntryPoint="ShareTarget.App">
        <uap:ShareTarget>
          <uap:SupportedFileTypes>
            <uap:SupportsAnyFileType />
          </uap:SupportedFileTypes>
          <uap:DataFormat>Bitmap</uap:DataFormat>
        </uap:ShareTarget>
      </uap:Extension>
</Extensions>  
```

UWP プロジェクトによって生成された実行可能ファイルの名前と、エントリ ポイント クラスの名前を指定します。 アプリとの間で共有できるようにするファイルの種類を指定することも必要です。

<a id="override" />

### <a name="override-the-onnavigatedto-event-handler"></a>OnNavigatedTo イベント ハンドラーをオーバーライドする

UWP プロジェクトの **App** クラスで、**OnNavigatedTo** イベント ハンドラーをオーバーライドします。

このイベント ハンドラーは、ユーザーがファイルを共有するためにアプリを選択するときに呼び出されます。

```csharp
protected override async void OnNavigatedTo(NavigationEventArgs e)
{
  this.shareOperation = (ShareOperation)e.Parameter;
  if (this.shareOperation.Data.Contains(StandardDataFormats.StorageItems))
  {
      this.sharedStorageItems =
        await this.shareOperation.Data.GetStorageItemsAsync();
       
      foreach (StorageFile item in this.sharedStorageItems)
      {
          ProcessSharedFile(item);
      }
  }
}
```

## <a name="create-a-background-task"></a>バックグラウンド タスクを作成する

バックグラウンド タスクを追加して、アプリが一時停止されているときでもコードを実行できます。 バックグラウンド タスクは、ユーザーの操作を必要としない小さなタスクに最適です。 たとえば、タスクはメールのダウンロード、受信チャット メッセージに関するトースト通知の表示、システムの状態の変化に対する対応を行うことができます。

バックグラウンド タスクを登録する WPF サンプル アプリを以下に示します。

![バックグラウンド タスク](images/desktop-to-uwp/sample-background-task.png)

タスクは http 要求を行い、要求が応答を返すのにかかる時間を測定します。 タスクはさらに興味深いものと考えられますが、このサンプルはバックグラウンド タスクの基本的なしくみを学習するのに適しています。

### <a name="have-a-closer-look-at-this-app"></a>このアプリを詳しく確認する

:heavy_check_mark: [コードを参照する](https://github.com/Microsoft/Windows-Packaging-Samples/tree/master/BGTask)

### <a name="the-design-pattern"></a>設計パターン

バックグラウンド サービスを作成するには、以下の手順を実行します。

:1: [バックグラウンド タスクの実装](#implement-task)

:2: [バックグラウンド タスクの構成](#configure-background-task)

:3: [バックグラウンド タスクの登録](#register-background-task)

<a id="implement-task" />

### <a name="implement-the-background-task"></a>バックグラウンド タスクの実装

Windows ランタイム コンポーネント プロジェクトにコードを追加することで、バックグラウンド タスクを実装します。

```csharp
public sealed class SiteVerifier : IBackgroundTask
{
    public async void Run(IBackgroundTaskInstance taskInstance)
    {

        taskInstance.Canceled += TaskInstance_Canceled;
        BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
        var msg = await MeasureRequestTime();
        ShowToast(msg);
        deferral.Complete();
    }

    private async Task<string> MeasureRequestTime()
    {
        string msg;
        try
        {
            var url = ApplicationData.Current.LocalSettings.Values["UrlToVerify"] as string;
            var http = new HttpClient();
            Stopwatch clock = Stopwatch.StartNew();
            var response = await http.GetAsync(new Uri(url));
            response.EnsureSuccessStatusCode();
            var elapsed = clock.ElapsedMilliseconds;
            clock.Stop();
            msg = $"{url} took {elapsed.ToString()} ms";
        }
        catch (Exception ex)
        {
            msg = ex.Message;
        }
        return msg;
    }
```

<a id="configure-background-task" />

### <a name="configure-the-background-task"></a>バックグラウンド タスクの構成

マニフェスト デザイナーで、ソリューションの UWP プロジェクトの **package.appxmanifest** ファイルを開きます。

**[宣言]** タブで、**[バックグラウンド タスク]** 宣言を追加します。

![バックグラウンド タスクのオプション](images/desktop-to-uwp/background-task-option.png)

次に、必要なプロパティを選択します。 サンプルでは、**Timer** プロパティを使います。

![Timer プロパティ](images/desktop-to-uwp/timer-property.png)

バックグラウンド タスクを実装する Windows ランタイム コンポーネントでクラスの完全修飾名を指定します。

![Timer プロパティ](images/desktop-to-uwp/background-task-entry-point.png)

<a id="register-background-task" />

### <a name="register-the-background-task"></a>バックグラウンド タスクの登録

バックグラウンド タスクを登録するデスクトップ アプリケーション プロジェクトにコードを追加します。

```csharp
public void RegisterBackgroundTask(String triggerName)
{
    var current = BackgroundTaskRegistration.AllTasks
        .Where(b => b.Value.Name == triggerName).FirstOrDefault().Value;

    if (current is null)
    {
        BackgroundTaskBuilder builder = new BackgroundTaskBuilder();
        builder.Name = triggerName;
        builder.SetTrigger(new MaintenanceTrigger(15, false));
        builder.TaskEntryPoint = "HttpPing.SiteVerifier";
        builder.Register();
        System.Diagnostics.Debug.WriteLine("BGTask registered:" + triggerName);
    }
    else
    {
        System.Diagnostics.Debug.WriteLine("Task already:" + triggerName);
    }
}
```
## <a name="support-and-feedback"></a>サポートとフィードバック

**質問に対する回答を見つける**

ご質問がある場合は、 Stack Overflow でお問い合わせください。 Microsoft のチームでは、これらの[タグ](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge)をチェックしています。 [こちら](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D)から質問することもできます。

**フィードバックの提供または機能の提案を行う**

[UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial) のページをご覧ください。