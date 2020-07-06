---
description: この記事では、XAML Islands を使用して WPF アプリで標準 UWP コントロールをホストする方法を示します。
title: XAML Islands を使用して WPF アプリで標準 UWP コントロールをホストする
ms.date: 01/24/2020
ms.topic: article
keywords: Windows 10, UWP, Windows フォーム, WPF, XAML Islands, ラップされたコントロール, 標準コントロール, InkCanvas, InkToolbar
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 1a2b5722ab836e715bce1b4c94fab97e6a28646e
ms.sourcegitcommit: cee2060bfc8489236e00e5951751bcc5bd632b0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84614908"
---
# <a name="host-a-standard-uwp-control-in-a-wpf-app-using-xaml-islands"></a>XAML Islands を使用して WPF アプリで標準 UWP コントロールをホストする

この記事では、[XAML Islands](xaml-islands.md) を使用して、WPF アプリで標準 UWP コントロール (つまり、Windows SDK によって提供されるファーストパーティ UWP コントロール) をホストする 2 つの方法について説明します。

* Windows Community Toolkit の[ラップされたコントロール](xaml-islands.md#wrapped-controls)を使用して、UWP の [InkCanvas](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) コントロールと [InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) コントロールをホストする方法を示します。 これらのコントロールでは、便利な UWP コントロールの小さいセットのインターフェイスと機能がラップされています。 これらを WPF プロジェクトまたは Windows フォーム プロジェクトのデザイン サーフェイスに直接追加し、他の WPF コントロールや Windows フォーム コントロールと同じようにデザイナーで使用できます。

* また、Windows Community Toolkit の [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) コントロールを使用して、UWP の [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) コントロールをホストする方法についても説明します。 ラップされたコントロールとして使用できるのは少数の UWP コントロールだけであり、他の標準 UWP コントロールをホストするには [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) を使用できます。

この記事では、WPF アプリで UWP コントロールをホストする方法について説明しますが、Windows フォーム アプリでもプロセスはほぼ同じです。

## <a name="required-components"></a>必要なコンポーネント

WPF (または Windows フォーム) アプリで UWP コントロールをホストするには、ソリューションに次のコンポーネントが必要です。 この記事では、これらの各コンポーネントを作成する手順について説明します。

* **アプリのプロジェクトとソース コード**。 [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) コントロールを使用した標準ファーストパーティ UWP コントロールのホストは、.NET Framework または .NET Core 3 をターゲットとするアプリでサポートされています。

* **XamlApplication から派生するルート Application クラスが定義されている UWP アプリ プロジェクト**。 WPF プロジェクトまたは Windows フォーム プロジェクトでは、カスタムの UWP XAML コントロールを検出て読み込めるように、Windows Community Toolkit によって提供される [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) クラスのインスタンスにアクセスできる必要があります。 これを行うには、WPF アプリまたは Windows フォーム アプリのソリューションの一部である別の UWP アプリ プロジェクト内でこのオブジェクトを定義することをお勧めします。 

    > [!NOTE]
    > ファーストパーティの UWP コントロールをホストするために `XamlApplication` オブジェクトは必要ありませんが、アプリでは、カスタム UWP コントロールのホストなど、XAML Island の全範囲のシナリオをサポートするために、このオブジェクトが必要です。 したがって、XAML Islands を使用するすべてのソリューションでは、常に `XamlApplication` オブジェクトを定義することをお勧めします。

    > [!NOTE]
    > `XamlApplication` オブジェクトは、ソリューション内の 1 つのプロジェクトだけで定義されている必要があります。 アプリ内のすべてのカスタム UWP コントロールで、同じ `XamlApplication` オブジェクトを共有します。 `XamlApplication` オブジェクトが定義されているプロジェクトには、XAML Island で UWP コントロールをホストするために使用される他のすべての UWP ライブラリとプロジェクトへの参照が含まれている必要があります。

## <a name="create-a-wpf-project"></a>WPF プロジェクトを作成する

作業を始める前に、次の手順に従って WPF プロジェクトを作成し、XAML Islands をホストするように構成します。 WPF プロジェクトが既にある場合は、以下の手順とコード例をプロジェクトに合わせて調整してかまいません。

1. Visual Studio 2019 で、新しい **WPF アプリ (.NET Framework)** プロジェクトまたは **WPF アプリ (.NET Core)** プロジェクトを作成します。 **WPF アプリ (.NET Core)** プロジェクトを作成する場合は、最初に最新バージョンの [.NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) をインストールする必要があります。

2. [パッケージ参照](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)が有効になっていることを確認します。

    1. Visual Studio で、 **[ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー設定]** の順にクリックします。
    2. **[既定のパッケージ管理形式]** で **[PackageReference]** が選択されていることを確認します。

3. **ソリューション エクスプローラー**で WPF プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

4. **[NuGet パッケージ マネージャー]** ウィンドウで、 **[プレリリースを含める]** が選択されていることを確認します。

5. **[参照]** タブを選択し、[Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) パッケージ (バージョン v 6.0.0 以降) を見つけて、パッケージをインストールします。 このパッケージでは、WPF でラップされた UWP コントロールを使用するために必要なものがすべて提供されています ([InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)、[InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar)、[WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) コントロールなど)。
    > [!NOTE]
    > Windows フォーム アプリでは、[Microsoft.Toolkit.Forms.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls) パッケージ (バージョン v6.0.0 以降) を使用する必要があります。

6. x86 や x64 などの特定のプラットフォームを対象とするようにソリューションを構成します。 XAML Islands のほとんどのシナリオは、 **[任意の CPU]** を対象とするプロジェクトではサポートされていません。

    1. **ソリューション エクスプローラー**で、ソリューション ノードを右クリックし、 **[プロパティ]**  ->  **[構成プロパティ]**  ->  **[構成マネージャー]** を選択します。 
    2. **[アクティブ ソリューション プラットフォーム]** で、 **[新規作成]** を選択します。 
    3. **[新しいソリューション プラットフォーム]** ダイアログで、 **[x64]** または **[x86]** を選択して、 **[OK]** をクリックします。 
    4. 開いているダイアログ ボックスを閉じます。

## <a name="define-a-xamlapplication-class-in-a-uwp-app-project"></a>UWP アプリ プロジェクトで XamlApplication クラスを定義する

次に、UWP アプリ プロジェクトをソリューションに追加し、このプロジェクトの既定の `App` クラスを、Windows Community Toolkit によって提供される [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) クラスから派生するように変更します。 このクラスは [IXamlMetadaraProvider](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.IXamlMetadataProvider) インターフェイスをサポートしています。これにより、アプリは実行時にアプリケーションの現在のディレクトリにある、アセンブリ内のカスタム UWP XAML コントロールのメタデータを検出して読み込むことができます。 このクラスでは、現在のスレッドの UWP XAML フレームワークも初期化されます。

> [!NOTE]
> このステップはファーストパーティの UWP コントロールをホストするには必要ありませんが、アプリでは、カスタム UWP コントロールのホストなど、XAML Island の全範囲のシナリオをサポートするために、`XamlApplication` オブジェクトが必要です。 したがって、XAML Islands を使用するすべてのソリューションでは、常に `XamlApplication` オブジェクトを定義することをお勧めします。

1. **ソリューション エクスプローラー**で、ソリューション ノードを右クリックし、 **[追加]**  ->  **[新しいプロジェクト]** を選択します。
2. ソリューションに **[空白のアプリ (ユニバーサル Windows)]** プロジェクトを追加します。 対象バージョンと最小バージョンの両方が **Windows 10 バージョン 1903** 以降に設定されていることを確認します。
3. UWP アプリ プロジェクトで、[Microsoft.Toolkit.Win32.UI.XamlApplication](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) NuGet パッケージ (version v 6.0.0 以降) をインストールします。
4. **App.xaml** ファイルを開き、このファイルの内容を次の XAML に置き換えます。 `MyUWPApp` を、UWP アプリ プロジェクトの名前空間に置き換えます。

    ```xml
    <xaml:XamlApplication
        x:Class="MyUWPApp.App"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xaml="using:Microsoft.Toolkit.Win32.UI.XamlHost"
        xmlns:local="using:MyUWPApp">
    </xaml:XamlApplication>
    ```

5. **App.xaml.cs** ファイルを開き、このファイルの内容を次のコードに置き換えます。 `MyUWPApp` を、UWP アプリ プロジェクトの名前空間に置き換えます。

    ```csharp
    namespace MyUWPApp
    {
        public sealed partial class App : Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication
        {
            public App()
            {
                this.Initialize();
            }
        }
    }
    ```

6. UWP アプリ プロジェクトから **MainPage.xaml** ファイルを削除します。
7. UWP アプリ プロジェクトをビルドします。
8. WPF プロジェクトで、UWP アプリ プロジェクトへの参照を追加します。 

    * WPF プロジェクトが .NET Core を対象としている場合は、 **[依存関係]** ノードを右クリックし、UWP アプリ プロジェクトへの参照を追加します。 
    * WPF プロジェクトが .NET Framework を対象としている場合は、プロジェクト ノードを右クリックし、 **[ビルド依存関係]**  ->  **[プロジェクトの依存関係]** の順に選択して、UWP アプリ プロジェクトを選択します。

## <a name="instantiate-the-xamlapplication-object-in-the-entry-point-of-your-wpf-app"></a>WPF アプリのエントリ ポイントで XamlApplication オブジェクトをインスタンス化する

次に、先に UWP プロジェクトで定義した `App` クラス (これは、`XamlApplication` から派生するようになったクラスです) のインスタンスを作成するためのコードを、WPF アプリのエントリ ポイントに追加します。

1. WPF プロジェクトでプロジェクト ノードを右クリックし、 **[追加]**  ->  **[新しい項目]** を選択して、 **[クラス]** を選択します。 クラスに **Program** という名前を指定し、 **[追加]** をクリックします。

2. 生成された `Program` クラスを次のコードに置き換えて、ファイルを保存します。 `MyUWPApp` を UWP アプリ プロジェクトの名前空間に置き換え、`MyWPFApp` を WPF アプリ プロジェクトの名前空間に置き換えます。

    ```csharp
    public class Program
    {
        [System.STAThreadAttribute()]
        public static void Main()
        {
            using (new MyUWPApp.App())
            {
                MyWPFApp.App app = new MyWPFApp.App();
                app.InitializeComponent();
                app.Run();
            }
        }
    }
    ```

3. プロジェクト ノードを右クリックし、 **[プロパティ]** を選択します。

4. プロパティの **[アプリケーション]** タブで、 **[スタートアップ オブジェクト]** ドロップダウンをクリックし、前のステップで追加した `Program` クラスの完全修飾名を選択します。 
    > [!NOTE]
    > 既定の WPF プロジェクトで生成されるコード ファイルでは、変更を意図されていない `Main` エントリ ポイント関数が定義されています。 このステップでは、プロジェクトのエントリ ポイントを新しい `Program` クラスの `Main` メソッドに変更します。これにより、アプリのスタートアップ プロセスの可能な限り早い段階で実行されるコードを追加できるようになります。 

5. プロジェクトのプロパティへの変更を保存します。

## <a name="host-an-inkcanvas-and-inktoolbar-by-using-wrapped-controls"></a>ラップされたコントロールを使用して InkCanvas と InkToolbar をホストする

UWP XAML Islands を使用するようにプロジェクトを構成したので、ラップされた UWP コントロール [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) および [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) をアプリに追加できます。

1. **ソリューション エクスプローラー**で、**MainWindow.xaml** ファイルを開きます。

2. XAML ファイルの先頭近くにある **Window** 要素に、次の属性を追加します。 これにより、ラップされた UWP コントロール [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) および [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) の XAML 名前空間が参照されるようになります。

    ```xml
    xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```

    この属性を追加した後の **Window** 要素は、次のようになります。

    ```xml
    <Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:WpfApp"
            xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            x:Class="WpfApp.MainWindow"
            mc:Ignorable="d"
            Title="MainWindow" Height="800" Width="800">
    ```

3. **MainWindow.xaml** ファイルで、既存の `<Grid>` 要素を次の XAML に置き換えます。 この XAML により、[InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) コントロールと [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) コントロール (名前空間として前に定義した **Controls** キーワードが前に付いているもの) が `<Grid>` に追加されます。

    ```xml
    <Grid Margin="10,50,10,10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <Controls:InkToolbar x:Name="myInkToolbar" TargetInkCanvas="{x:Reference myInkCanvas}" Grid.Row="0" Width="300"
            Height="50" Margin="10,10,10,10" HorizontalAlignment="Left" VerticalAlignment="Top" />
        <Controls:InkCanvas x:Name="myInkCanvas" Grid.Row="1" HorizontalAlignment="Left" Width="600" Height="400"
            Margin="10,10,10,10" VerticalAlignment="Top" />
    </Grid>
    ```

    > [!NOTE]
    > また、 **[ツールボックス]** の **[Windows Community Toolkit]** セクションからデザイナーにドラッグすることで、これらのコントロールや他のラップされたコントロールをウィンドウに追加することもできます。

4. **MainWindow.xaml** ファイルを保存します。

    Surface のようなデジタル ペンをサポートするデバイスがあり、このラボを物理コンピューターで実行している場合は、アプリをビルドして実行し、ペンを使用して画面にデジタル インクを描画できます。 一方、ペン対応のデバイスを使用していない場合は、マウスで署名しようとしても、何も起こりません。 これは、**InkCanvas** コントロールがデジタル ペンに対してのみ既定で有効になるためです。 ただし、この動作は変更できます。

5. **MainWindow.xaml.cs** ファイルを開きます。

6. ファイルの先頭に、次の名前空間宣言を追加します。

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```

7. `MainWindow()` コンストラクターを探します。 `InitializeComponent()` メソッドの後に次のコード行を追加し、コード ファイルを保存します。

    ```csharp
    this.myInkCanvas.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```

    **InkPresenter** オブジェクトを使用して、既定のインク エクスペリエンスをカスタマイズできます。 このコードでは、**InputDeviceTypes** プロパティを使用して、マウスおよびペン入力を有効にします。

8. F5 キーを再度押して、デバッガーでアプリをリビルドして実行します。 マウスでコンピューターを使用している場合は、インク キャンバス スペースにマウスを使用して何かを描画できることを確認します。

## <a name="host-a-calendarview-by-using-the-host-control"></a>ホスト コントロールを使用して CalendarView をホストする

ラップされた UWP コントロール [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) と [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) をアプリに追加したので、[WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) コントロールを使用して、アプリに [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) を追加できるようになりました。

> [!NOTE]
> [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) コントロールは、[Microsoft.Toolkit.Wpf.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost) パッケージによって提供されます。 このパッケージは、前にインストールした [Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) パッケージに含まれています。

1. **ソリューション エクスプローラー**で、**MainWindow.xaml** ファイルを開きます。

2. XAML ファイルの先頭近くにある **Window** 要素に、次の属性を追加します。 これにより、[WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) コントロールの XAML 名前空間が参照されるようになります。

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    この属性を追加した後の **Window** 要素は、次のようになります。

    ```xml
    <Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:WpfApp"
            xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            x:Class="WpfApp.MainWindow"
            mc:Ignorable="d"
            Title="MainWindow" Height="800" Width="800">
    ```

4. **MainWindow.xaml** ファイルで、既存の `<Grid>` 要素を次の XAML に置き換えます。 この XAML では、グリッドに行が追加され、最後の行に [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) オブジェクトが追加されます。 UWP の [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) コントロールをホストするため、この XAML では `InitialTypeName` プロパティにコントロールの完全修飾名を設定します。 この XAML では、ホストされているコントロールがレンダリングされると発生する `ChildChanged` イベントのイベント ハンドラーも定義されています。

    ```xml
    <Grid Margin="10,50,10,10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <Controls:InkToolbar x:Name="myInkToolbar" TargetInkCanvas="{x:Reference myInkCanvas}" Grid.Row="0" Width="300"
            Height="50" Margin="10,10,10,10" HorizontalAlignment="Left" VerticalAlignment="Top" />
        <Controls:InkCanvas x:Name="myInkCanvas" Grid.Row="1" HorizontalAlignment="Left" Width="600" Height="400" 
            Margin="10,10,10,10" VerticalAlignment="Top" />
        <xamlhost:WindowsXamlHost x:Name="myCalendar" InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Row="2" 
              Margin="10,10,10,10" Width="600" Height="300" ChildChanged="MyCalendar_ChildChanged"  />
    </Grid>
    ```

5. **MainWindow.xaml** ファイルを保存し、**MainWindow.xaml.cs** ファイルを開きます。

7. ファイルの先頭に、次の名前空間宣言を追加します。

    ```csharp
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```

10. 次の `ChildChanged` イベント ハンドラー メソッドを `MainWindow` クラスに追加し、コード ファイルを保存します。 ホスト コントロールがレンダリングされると、このイベント ハンドラーが実行され、カレンダー コントロールの `SelectedDatesChanged` イベント用の簡単なイベント ハンドラーが作成されます。

    ```csharp
    private void MyCalendar_ChildChanged(object sender, EventArgs e)
    {
        WindowsXamlHost windowsXamlHost = (WindowsXamlHost)sender;

        Windows.UI.Xaml.Controls.CalendarView calendarView =
            (Windows.UI.Xaml.Controls.CalendarView)windowsXamlHost.Child;

        if (calendarView != null)
        {
            calendarView.SelectedDatesChanged += (obj, args) =>
            {
                if (args.AddedDates.Count > 0)
                {
                    MessageBox.Show("The user selected a new date: " + 
                        args.AddedDates[0].DateTime.ToString());
                }
            };
        }
    }
    ```

11. F5 キーを再度押して、デバッガーでアプリをリビルドして実行します。 カレンダー コントロールがウィンドウの下部に表示されることを確認します。

## <a name="package-the-app"></a>アプリをパッケージ化する

必要に応じて、配置用に WPF アプリを [MSIX パッケージ](https://docs.microsoft.com/windows/msix)にパッケージ化することもできます。 MSIX は、Windows 向けの最新のアプリ パッケージ化テクノロジであり、MSI、.appx、App-V、ClickOnce インストールの各テクノロジの組み合わせが基になっています。

次の手順では、Visual Studio 2019 で [Windows アプリケーション パッケージ プロジェクト](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net)を使用して、ソリューションのすべてのコンポーネントを MSIX パッケージにパッケージ化する方法について説明します。 これらの手順は、WPF アプリを MSIX パッケージにパッケージ化する場合にのみ必要です。

> [!NOTE]
> 配置用に [MSIX パッケージ](https://docs.microsoft.com/windows/msix)にアプリケーションをパッケージ化しない場合は、アプリを実行するコンピューターに [Visual C++ ランタイム](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)がインストールされている必要があります。

1. ソリューションに新しい [Windows アプリケーション パッケージ プロジェクト](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net)を追加します。 プロジェクトを作成するときに、 **[ターゲット バージョン]** と **[最小バージョン]** の両方に対して、**Windows 10 バージョン 1903 (10.0、ビルド 18362)** を選択します。

2. パッケージ プロジェクトで、 **[アプリケーション]** ノードを右クリックして **[参照の追加]** を選択します。 プロジェクトの一覧でソリューション内の WPF プロジェクトを選択し、 **[OK]** をクリックします。

3. x86 や x64 などの特定のプラットフォームを対象とするようにソリューションを構成します。 Windows アプリケーション パッケージ プロジェクトを使用して MSIX パッケージに WPF アプリをビルドするには、このようにする必要があります。

    1. **ソリューション エクスプローラー**で、ソリューション ノードを右クリックし、 **[プロパティ]**  ->  **[構成プロパティ]**  ->  **[構成マネージャー]** を選択します。
    2. **[アクティブ ソリューション プラットフォーム]** で、 **[x64]** または **[x86]** を選択します。
    3. WPF プロジェクトの行の **[プラットフォーム]** 列で、 **[新規]** を選択します。
    4. **[新しいソリューション プラットフォーム]** ダイアログで、 **[x64]** または **[x86]** ( **[アクティブ ソリューション プラットフォーム]** に対して選択したものと同じプラットフォーム) を選択し、 **[OK]** をクリックします。
    5. 開いているダイアログ ボックスを閉じます。

5. パッケージ プロジェクトをビルドして実行します。 WPF が実行され、UWP カスタム コントロールが想定どおりに表示されることを確認します。

## <a name="related-topics"></a>関連トピック

* [デスクトップ アプリで UWP XAML コントロールをホストする (XAML Islands)](xaml-islands.md)
* [XAML Islands コード サンプル](https://github.com/microsoft/Xaml-Islands-Samples)
* [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)
* [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar)
* [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost)
