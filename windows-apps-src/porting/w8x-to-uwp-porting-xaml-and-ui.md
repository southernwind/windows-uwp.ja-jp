---
author: mcleblanc
description: 宣言型 XAML マークアップ形式での UI の定義は、ユニバーサル 8.1 アプリからユニバーサル Windows プラットフォーム (UWP) アプリに適切に変換されます。
title: Windows ランタイム 8.x の XAML と UI の UWP への移植
ms.assetid: 78b86762-7359-474f-b1e3-c2d7cf9aa907
ms.author: markl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP
ms.openlocfilehash: 19a6ef29265c22d1bb02464a76ab20e487c67ce4
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.locfileid: "247034"
---
# <a name="porting-windows-runtime-8x-xaml-and-ui-to-uwp"></a>Windows ランタイム 8.x の XAML と UI の UWP への移植

\[Windows 10 の UWP アプリ向けに更新。 Windows 8.x の記事については、[アーカイブ](http://go.microsoft.com/fwlink/p/?linkid=619132)をご覧ください\]

前のトピックは、「[トラブルシューティング](w8x-to-uwp-troubleshooting.md)」でした。

宣言型 XAML マークアップ形式での UI の定義は、ユニバーサル 8.1 アプリからユニバーサル Windows プラットフォーム (UWP) アプリに適切に変換されます。 ほとんどのマークアップには互換性がありますが、場合によっては、使っているシステムのリソース キーやカスタム テンプレートを調整する必要があります。 ビュー モデルの命令型コードについては、ほとんどあるいはまったく変更する必要はありません。 プレゼンテーション層にあるほとんどのコード (UI 要素を操作するコード) も、簡単に移植できます。

## <a name="imperative-code"></a>命令型コード

プロジェクトのビルド段階に進むだけであれば、重要でないコードのコメントアウトやスタブの挿入を行うことができます。 次に、このセクションの以降のトピック (および前のトピック「[トラブルシューティング](w8x-to-uwp-troubleshooting.md)」) を参考にして、ビルドとランタイムの問題が解決して移植が完了するまで一度に 1 つの問題について反復作業を行います。

## <a name="adaptiveresponsive-ui"></a>アダプティブ/応答性の高い UI

アプリは、画面サイズと解像度が異なるさまざまなデバイスで実行できる場合があります。このため、最小限の手順でアプリを移植するだけでなく、各デバイスで最適な外観になるように UI を調整する必要があります。 アダプティブな Visual State Manager の機能を使って、ウィンドウのサイズを動的に検出し、それに応じてレイアウトを変更できます。その方法を示す例を、Bookstore2 ケース スタディの「[アダプティブ UI](w8x-to-uwp-case-study-bookstore2.md)」に示します。

## <a name="back-button-handling"></a>"戻る" ボタンの処理

ユニバーサル 8.1 アプリ、Windows ストア アプリ、Windows Phone ストア アプリには、表示する UI と "戻る" ボタンで処理するイベントについてさまざまなアプローチがあります。 ただし、Windows 10 アプリでは、1 つのアプローチをアプリで使うことができます。 モバイル デバイスでは、このボタンはデバイス上の静電容量式のボタンまたはシェル内のボタンとして提供されます。 デスクトップ デバイスでは、アプリ内で戻るナビゲーションが可能な場合には常にアプリのクロムにボタンを追加します。このボタンは、ウィンドウ表示されたアプリのタイトル バーまたはタブレット モードのタスク バーに表示されます。 "戻る" ボタンのイベントはすべてのデバイス ファミリに共通するユニバーサルな概念であり、ハードウェアまたはソフトウェアに実装されるボタンは同じ [**BackRequested**](https://msdn.microsoft.com/library/windows/apps/dn893596) イベントを発生させます。

次の例は、すべてのデバイス ファミリで動作し、同じ処理をすべてのページに適用する場合や、ナビゲーションを確認する必要がない場合 (保存していない変更に関する警告を表示する場合など) に適しています。

```csharp
   // app.xaml.cs

    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        [...]

        Windows.UI.Core.SystemNavigationManager.GetForCurrentView().BackRequested += App_BackRequested;
        rootFrame.Navigated += RootFrame_Navigated;
    }

    private void RootFrame_Navigated(object sender, NavigationEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        // Note: On device families that have no title bar, setting AppViewBackButtonVisibility can safely execute 
        // but it will have no effect. Such device families provide back button UI for you.
        if (rootFrame.CanGoBack)
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Visible;
        }
        else
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Collapsed;
        }
    }

    private void App_BackRequested(object sender, Windows.UI.Core.BackRequestedEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        if (rootFrame.CanGoBack)
        {
            rootFrame.GoBack();
        }
    }
```

プログラムを使ったアプリの終了に関しても、すべてのデバイス ファミリに対する単一のアプローチがあります。

```csharp
   Windows.UI.Xaml.Application.Current.Exit();
```

## <a name="charms"></a>チャーム

チャームと統合されるコードを変更する必要はありません。ただし、いくつかの UI をアプリに追加して、Windows 10 シェルに含まれていないチャーム バーの代わりに使う必要があります。 Windows 10 で実行されるユニバーサル 8.1 アプリには、独自の代替 UI があります。このような UI は、アプリのタイトル バーにシステムが表示するクロムによって提供されます。

## <a name="controls-and-control-styles-and-templates"></a>コントロールとコントロール スタイルおよびテンプレート

Windows 10 で実行されるユニバーサル 8.1 アプリでは、コントロールに関して 8.1 の外観と動作が維持されます。 ただし、そのアプリを Windows 10 アプリに移植すると、外観と動作が異なる場合があるので、注意してください。 コントロールのアーキテクチャと設計は、Windows 10 アプリでは基本的に変更はありません。ほとんどの変更は、[デザイン言語](#design-language-in-windows-10)、単純化、操作性の向上に関連するものです。

**注**   PointerOver 表示状態は、Windows 10 アプリと Windows ストア アプリのカスタム スタイル/テンプレートには関連しますが、Windows Phone ストア アプリのカスタム スタイル/テンプレートには関連しません。 このことから (および Windows 10 アプリでサポートされているシステム リソース キーのため)、アプリを Windows 10 に移植するときは、Windows ストア アプリからカスタム スタイル/テンプレートを再利用することをお勧めします。
カスタムのスタイル/テンプレートで、確実に最新の表示状態のセットを使い、既定のスタイル/テンプレートに対するパフォーマンスの向上を活用するには、Windows 10 の新しい既定のテンプレートのコピーを編集し、カスタマイズを再適用します。 パフォーマンス向上の 1 つの例として、以前に **ContentPresenter** または Panel を囲んでいた **Border** が削除され、子要素が境界線を表示するようになりました。

以下に、コントロールの変更に関する具体的な例を示します。

| コントロール名 | 変更点 |
|--------------|--------|
| **AppBar**   | **AppBar** コントロールを使っている場合 (代わりに [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) をお勧めします)、既定では、Windows 10 アプリでこのコントロールは非表示になりません。 これを制御するには、[**AppBar.ClosedDisplayMode**](https://msdn.microsoft.com/library/windows/apps/dn633872) プロパティを使います。 |
| **AppBar**、[**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | Windows 10 アプリでは、**AppBar** と [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) には **[詳細表示]** ボタン (省略記号で表されます) があります。 |
| [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | Windows ストア アプリでは、[**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) のセカンダリ コマンドが常に表示されます。 Windows Phone ストア アプリと Windows 10 アプリでは、コマンド バーが開くまで表示されません。 |
| [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | Windows Phone ストア アプリでは、[**CommandBar.IsSticky**](https://msdn.microsoft.com/library/windows/apps/hh701944) の値は、バーの簡易非表示の動作に影響しません。 Windows 10 アプリでは、**IsSticky** が true に設定されると、**CommandBar** は簡易非表示のジェスチャを無視します。 |
| [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | Windows 10 アプリでは、[**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) で [**EdgeGesture.Completed**](https://msdn.microsoft.com/library/windows/apps/hh701622) イベントも [**UIElement.RightTapped**](https://msdn.microsoft.com/library/windows/apps/br208984) イベントも処理されません。 タップまたはスワイプにも応答しません。 ただし、これらのイベントを処理し、[**IsOpen**](https://msdn.microsoft.com/library/windows/apps/hh701939) を設定するオプションがあります。 |
| [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584)、[**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280) | [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584) や [**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280) に加えられた視覚的な変化によってアプリの外観がどうなるかを確認してください。 モバイル デバイスで実行される Windows 10 アプリでは、これらのコントロールを使っても選択ページへは移動されません。ただし、簡易非表示に対応したポップアップが使われます。 |
| [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584)、[**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280) | Windows 10 アプリでは、[**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584) または [**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280) をポップアップ内に配置することはできません。 ポップアップ型のコントロールにこれらのコントロールを表示する場合は、[**DatePickerFlyout**](https://msdn.microsoft.com/library/windows/apps/dn625013) と [**TimePickerFlyout**](https://msdn.microsoft.com/library/windows/apps/dn608313) を使うことができます。 |
| **GridView**、**ListView** | **GridView**/**ListView** については、「[GridView と ListView の変更](#gridview-and-listview-changes)」をご覧ください。 |
| [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) | Windows Phone ストア アプリでは、[**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) コントロールは最後のセクションから最初のセクションに折り返します。 Windows ストア アプリと Windows 10 アプリでは、ハブ セクションは折り返しません。 |
| [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) | Windows Phone ストア アプリでは、[**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) コントロールの背景画像は、ハブ セクションに対する視差効果で移動します。 Windows ストア アプリと Windows 10 アプリでは、視差効果は使われません。 |
| [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843)  | ユニバーサル 8.1 アプリでは、[**HubSection.IsHeaderInteractive**](https://msdn.microsoft.com/library/windows/apps/dn251917) プロパティにより、セクション ヘッダーとその横に表示される山形のグリフが対話型になります。 Windows 10 アプリでは、ヘッダーの横に対話型の "詳細表示" アフォーダンスが表示されますが、ヘッダーそのものは対話型にはなりません。 **IsHeaderInteractive** により、操作で [**Hub.SectionHeaderClick**](https://msdn.microsoft.com/library/windows/apps/dn251953) イベントが発生するかどうかが決まります。 |
| **MessageDialog** | **MessageDialog** を使っている場合は、柔軟性が向上した [**ContentDialog**](https://msdn.microsoft.com/library/windows/apps/dn633972) の利用を検討してください。 [XAML UI の基本](http://go.microsoft.com/fwlink/p/?linkid=619992) のサンプルに関するページもご覧ください。 |
| **ListPickerFlyout**、**PickerFlyout**  | **ListPickerFlyout** と **PickerFlyout** は、Windows 10 アプリでは使われなくなりました。 単一選択ポップアップの場合は、[**MenuFlyout**](https://msdn.microsoft.com/library/windows/apps/dn299030) を使います。より複雑なエクスペリエンスの場合は、[**Flyout**](https://msdn.microsoft.com/library/windows/apps/dn279496) を使います。 |
| [**PasswordBox**](https://msdn.microsoft.com/library/windows/apps/br227519) | [**PasswordBox.IsPasswordRevealButtonEnabled**](https://msdn.microsoft.com/library/windows/apps/hh702579) プロパティは、Windows 10 アプリでは使われなくなり、設定しても効果はありません。 代わりに [**PasswordBox.PasswordRevealMode**](https://msdn.microsoft.com/library/windows/apps/dn890867) を使います。このプロパティでは、既定で **Peek** が設定されます (これにより、Windows ストア アプリのようにパスワードを表示するボタンが表示されます)。 「[パスワード ボックスのガイドライン](https://msdn.microsoft.com/library/windows/apps/dn596103)」もご覧ください。 |
| [**Pivot**](https://msdn.microsoft.com/library/windows/apps/dn608241) | [**Pivot**](https://msdn.microsoft.com/library/windows/apps/dn608241) はユニバーサル コントロールとなり、モバイル デバイスでの利用のみに限定されていた制限が排除されました。 |
| [**SearchBox**](https://msdn.microsoft.com/library/windows/apps/dn252771) | ユニバーサル デバイス ファミリでは [**SearchBox**](https://msdn.microsoft.com/library/windows/apps/dn252803) が実装されていますが、モバイル デバイスでは部分的に機能しません。 「[AutoSuggestBox に使用されない SearchBox](#searchbox-deprecated-in-favor-of-autosuggestbox)」をご覧ください。 |
| **SemanticZoom** | **SemanticZoom** については、「[SemanticZoom に関する変更](#semanticzoom-changes)」をご覧ください。 |
| [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/br209527)  | [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/br209527) の既定のプロパティの一部が変更されています。 [**HorizontalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209549) が **Auto**、[**VerticalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209589) が **Auto**、[**ZoomMode**](https://msdn.microsoft.com/library/windows/apps/br209601) が **Disabled** です。 新しい既定値がアプリに対して適切でない場合は、スタイルで変更するか、コントロール自体のローカル値として変更できます。  |
| [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) | Windows ストア アプリでは、スペル チェックは既定で [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) に対してオフになります。 Windows Phone ストア アプリと Windows 10 アプリでは、既定でオンになります。 |
| [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) | [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) の既定のフォント サイズは 11 から 15 に変更されました。 |
| [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) | [**TextBox.TextReadingOrder**](https://msdn.microsoft.com/library/windows/apps/dn252859) の既定値は、**Default** から **DetectFromContent** に変更されました。 これが望ましくない場合は、**UseFlowDirection** を使ってください。 **Default** は使われなくなりました。 |
| Various | アクセント カラーは Windows Phone ストア アプリと Windows 10 アプリに適用されますが、Windows ストア アプリには適用されません。  |

UWP アプリのコントロールについて詳しくは、「[機能別コントロール](https://msdn.microsoft.com/library/windows/apps/mt185405)」、「[コントロールの一覧](https://msdn.microsoft.com/library/windows/apps/mt185406)」、「[コントロールのガイドライン](https://msdn.microsoft.com/library/windows/apps/dn611856)」をご覧ください。

##  <a name="design-language-in-windows-10"></a>Windows 10 でのデザイン言語

ユニバーサル 8.1 アプリと Windows 10 アプリでは、デザイン言語に関して若干の違いがありますが、重要な相違点なので注意してください。 詳しくは、「[Design](http://dev.windows.com/design)」(設計) をご覧ください。 デザイン言語に変更が加えられていますが、設計原則は維持されています。細部にまで注意を払いながら、簡潔さを追求しています。そのために、クロムよりもコンテンツを優先し、視覚要素を大幅に減らし、真のデジタル領域を常に意識しています。また、視覚的な階層の利用 (特に文字体裁に対して)、グリッド内でのデザイン、滑らかなアニメーションを使ったエクスペリエンスの実現も行っています。

## <a name="effective-pixels-viewing-distance-and-scale-factors"></a>有効ピクセル、視聴距離、スケール ファクター

これまで、表示ピクセルは、デバイスの実際の物理サイズと解像度から UI 要素のサイズとレイアウトを抽象化する方法でした。 現在では、表示ピクセルが有効ピクセルに変わりました。その用語の説明、有効ピクセルが何をするものなのか、および有効ピクセルで使うことができる追加の値について、以下に示します。

一般的な考えとは異なり、"解像度" という用語はピクセル密度の測定値を表しており、ピクセル数ではありません。 "有効解像度" は、画像またはグリフを構成する物理ピクセルを解決して、デバイスの視聴距離と物理ピクセル サイズでの目視による相違の度合を取得する方法です (物理ピクセル サイズの逆数であるピクセル密度)。 有効解像度は、ユーザー中心であるために、エクスペリエンスの構築に適したメトリックです。 すべての要因について理解し、UI 要素のサイズを制御することによって、ユーザーのエクスペリエンスを適切なものにすることができます。

デバイスによって、有効ピクセルの幅の値が異なります。その範囲は、320 epx (最小のデバイス) から 1024 epx (一般的なサイズのモニター)、またはそれ以上のさらに広い幅になります。 これまでと同様に、自動的にサイズ調整される要素と動的レイアウト パネルを引き続き使うことで十分に対応できます。 ただし、場合によっては、UI 要素のプロパティを XAML マークアップで固定サイズに設定することがあります。 スケール ファクターは、アプリが実行されているデバイスやユーザーが行った表示設定に応じて、アプリに自動的に適用されます。 スケール ファクターによって、さまざまな画面サイズでユーザーに対してほぼ一定サイズのタッチ (または読み取り) ターゲットを提示するように、すべての UI 要素を固定サイズで維持できます。 また、動的レイアウトと共に使うと、UI は単にさまざまなデバイスで光学的なスケーリングを行うだけではありません。 利用可能な領域に対して適切な量のコンテンツを表示するために必要な処理も実行します。

すべてのディスプレイで最適なアプリのエクスペリエンスが実現できるように、一連のサイズで各ビットマップ アセットを作成し、各アセットが特定のスケール ファクターに適合するように設定することをお勧めします。 ただし、100% スケール、200% スケール、および 400% スケール (この優先順位で) でアセットを作成するほうが、多くの場合、すべての中間スケール ファクターで適切な結果を得ることができます。

**注**  何らかの理由で複数のサイズでアセットを作成できない場合は、100% スケールのアセットを作成します。 Microsoft Visual Studio では、UWP アプリの既定のプロジェクト テンプレートには 1 つのサイズのみのブランド アセット (タイル イメージとロゴ) が用意されていますが、これらは 100% のスケールではありません。 独自のアプリのアセットを作成する場合は、このセクションに示したガイドラインに従って、100%、200%、400% のサイズを用意し、アセット パックを使います。

複雑なアートワークがある場合は、さらに多くのサイズに対応したアセットが必要になることがあります。 ベクター アートを使って作業を始める場合は、どのようなスケール ファクターでも高品質なアセットを比較的簡単に生成できます。

Windows 10 アプリ向けのすべてスケール ファクターは 100%、125%、150%、200%、250%、300%、400% ですが、すべてのスケール ファクターをサポートすることはお勧めしません。 すべてのスケール ファクターのアセットを提供した場合、ストアでは、各デバイスに合った適切なサイズのアセットが選ばれ、それらのアセットのみがダウンロードされます。 ストアでは、デバイスの DPI に基づいて、ダウンロードするアセットが選ばれます。 140%% や 220%% などのスケール ファクターで Windows ストア アプリのアセットを再利用できますが、アプリは新しいスケール ファクターのいずれかで実行されるため、一部のビットマップ スケーリングを回避できなくなります。 状況に合った最適な結果を得ることができるかどうかを確認するために、さまざまなデバイスでアプリをテストしてください。

Windows ストア アプリの XAML マークアップを再利用できます。Windows ストア アプリでは、リテラルの寸法値がマークアップで使われています (図形や他の要素に関するサイズの設定や、文字体裁のために使われている場合があります)。 ただし、場合によっては、ユニバーサル 8.1 アプリ用のデバイスに比べ、Windows 10 アプリ用のデバイスではより大きなスケール ファクターが使われます (たとえば、140%% であった場合に 150%%、180%% であった場合に 200%% など)。 したがって、Windows 10 でこれらのリテラル値が大きすぎる場合は、それらの値に 0.8 を乗算してみてください。 詳しくは、「[UWP アプリ用レスポンシブ デザイン 101](https://msdn.microsoft.com/library/windows/apps/dn958435)」をご覧ください。

## <a name="gridview-and-listview-changes"></a>GridView と ListView の変更

コントロールを縦方向へスクロールするために、[**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705) の既定の style setter に対していくつかの変更が行われました (横方向へのスクロールではありません。これについては既定で対応しています)。 プロジェクトに含まれている既定のスタイルのコピーを編集した場合、そのコピーにはこれらの変更が適用されていないため、手動で変更を加える必要があります。 一連の変更を以下にまとめます。

-   [**ScrollViewer.HorizontalScrollBarVisibility**](https://msdn.microsoft.com/library/windows/apps/br209547) の setter は、**Auto** から **Disabled** に変更されました。
-   [**ScrollViewer.VerticalScrollBarVisibility**](https://msdn.microsoft.com/library/windows/apps/br209587) の setter は、**Disabled** から **Auto** に変更されました。
-   [**ScrollViewer.HorizontalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209549) の setter は、**Enabled** から **Disabled** に変更されました。
-   [**ScrollViewer.VerticalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209589) の setter は、**Disabled** から **Enabled** に変更されました。
-   [**ItemsPanel**](https://msdn.microsoft.com/library/windows/apps/br242826) の setter では、[**ItemsWrapGrid.Orientation**](https://msdn.microsoft.com/library/windows/apps/dn298907) の値が **Vertical** から **Horizontal** に変更されました。

最後の変更 (**Orientation** に対する変更) は矛盾していると考えられますが、ここでは折り返しグリッドについて説明していることに注意してください。 横方向の折り返しグリッド (新しい値) は、テキストが横方向に表示され、ページの終端で次の行に改行される書記体系と類似しています。 そのようなテキストのページでは、縦方向にスクロールします。 これに対して、縦方向の折り返しグリッド (以前の値) は、テキストが縦方向に表示される書記体系と類似しています。このため、横方向にスクロールします。

変更されたおよび Windows 10 でサポートされていない [**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705) と [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) の特性を次に示します。

-   Windows 10 アプリでは、[**IsSwipeEnabled**](https://msdn.microsoft.com/library/windows/apps/hh702518) プロパティはサポートされません (Windows ストア アプリのみ)。 API は存在しますが、API を設定しても効果はありません。 これまでのすべての選択ジェスチャがサポートされていますが、下方向へのスワイプと右クリックはサポートされていません。これは、下方向へのスワイプではデータが検出不可能と示されるためです。また、右クリックはコンテキスト メニューの表示用に予約されているためです。
-   Windows 10 アプリでは、[**ReorderMode**](https://msdn.microsoft.com/library/windows/apps/dn625099) プロパティはサポートされません (Windows Phone ストア アプリのみ)。 API は存在しますが、API を設定しても効果はありません。 代わりに、**GridView** や **ListView** に対して [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/br208912) と [**CanReorderItems**](https://msdn.microsoft.com/library/windows/apps/br242882) を true に設定することで、ユーザーは、長押し (またはクリックしてドラッグ) のジェスチャを使って順序を変更することができます。
-   Windows 10 向けの開発を行うとき、[**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) および [**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705) のどちらに対しても、アイテム コンテナーのスタイルでは、[**GridViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/dn279298) の代わりに [**ListViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/dn298500) を使います。 アイテム コンテナーの既定のスタイルをコピーして編集する場合は、適切な種類のスタイルを取得できます。
-   Windows 10 アプリでは、選択ビジュアルが変更されました。 [**SelectionMode**](https://msdn.microsoft.com/library/windows/apps/br242915) を **Multiple** に設定すると、既定では、各項目に対してチェック ボックスが表示されます。 **ListView** 項目の既定の設定では、チェック ボックスが項目の横にインラインで配置されます。その結果、他の項目が占める領域が若干小さくなり、移動されます。 **GridView** 項目の場合、既定では、チェック ボックスが項目の上部に重なって表示されます。 ただしどちらの場合も、チェック ボックスのレイアウト (インラインまたはオーバーレイ) を制御できます ([**CheckMode**](https://msdn.microsoft.com/library/windows/apps/dn913923) プロパティを使う)。また、アイテム コンテナーのスタイル内にある [**ListViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.aspx) 要素上にチェック ボックスをすべて表示するかどうかを制御することもできます ([**SelectionCheckMarkVisualEnabled**](https://msdn.microsoft.com/library/windows/apps/dn298541) プロパティを使う)。次に例を示します。
-   Windows 10 では、[**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/dn298914) イベントは UI の仮想化中に項目ごとに 2 回発生します。1 回は回収のためで、1 回は再利用のためです。 [**InRecycleQueue**](https://msdn.microsoft.com/library/windows/apps/dn279443) の値が **true** で、特別な回収操作がない場合、同じ項目が再利用されるとき (**InRecycleQueue** が **false** になる場合) には再入力されることが確実であれば、すぐにイベント ハンドラーを終了することができます。

```xml
<Style x:Key="CustomItemContainerStyle" TargetType="ListViewItem|GridViewItem">
    ...
    <Setter.Value>
        <ControlTemplate TargetType="ListViewItem|GridViewItem">
            <ListViewItemPresenter CheckMode="Inline|Overlay" ... />
        </ControlTemplate>
    </Setter.Value>
    ...
</Style>
```

![インライン チェック ボックスを使った ListViewItemPresenter](images/w8x-to-uwp-case-studies/ui-listviewbase-cb-inline.jpg)

インライン チェック ボックスを使った ListViewItemPresenter

![重ねて表示されるチェック ボックスを使った ListViewItemPresenter](images/w8x-to-uwp-case-studies/ui-listviewbase-cb-overlay.jpg)

重ねて表示されるチェック ボックスを使った ListViewItemPresenter

-   選択する際の下方向へのスワイプと右クリックのジェスチャがサポートされなくなったため (理由については上記のとおり)、相互作用モデルが変更されました。その結果の 1 つとして、[**ItemClick**](https://msdn.microsoft.com/library/windows/apps/br242904) イベントと [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776) イベントは相互に排他的ではなくなりました。 ご自分の Windows 10 アプリについて、シナリオを確認し、”選択” の相互作用モデルを採用するか、"呼び出し" の相互作用モデルを採用するかを決めてください 詳しくは、「[操作モードを変更する方法](https://msdn.microsoft.com/library/windows/apps/xaml/hh780625)」をご覧ください。
-   [**ListViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.aspx) のスタイルを指定する際に使うプロパティについて、変更がいくつかあります。 新しいプロパティは、[**CheckBoxBrush**](https://msdn.microsoft.com/library/windows/apps/dn913905)、[**PressedBackground**](https://msdn.microsoft.com/library/windows/apps/dn913931)、[**SelectedPressedBackground**](https://msdn.microsoft.com/library/windows/apps/dn913937)、[**FocusSecondaryBorderBrush**](https://msdn.microsoft.com/library/windows/apps/dn898370) です。 Windows 10 アプリで無視されるプロパティは、[**Padding**](https://msdn.microsoft.com/library/windows/apps/dn424775) (代わりに [**ContentMargin**](https://msdn.microsoft.com/library/windows/apps/dn424773) を使います)、[**CheckHintBrush**](https://msdn.microsoft.com/library/windows/apps/dn298504)、[**CheckSelectingBrush**](https://msdn.microsoft.com/library/windows/apps/dn298506)、[**PointerOverBackgroundMargin**](https://msdn.microsoft.com/library/windows/apps/dn424778)、[**ReorderHintOffset**](https://msdn.microsoft.com/library/windows/apps/dn298528)、[**SelectedBorderThickness**](https://msdn.microsoft.com/library/windows/apps/dn298533)、[**SelectedPointerOverBorderBrush**](https://msdn.microsoft.com/library/windows/apps/dn298539) です。

次の表では、[**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/br242919) コントロール テンプレートと [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/hh738501) コントロール テンプレートでの表示状態や表示状態グループに対する変更について説明します。

| 8.1                 |                         | Windows 10        |                     |
|---------------------|-------------------------|-------------------|---------------------|
| CommonStates        |                         | CommonStates      |                     |
|                     | Normal                  |                   | Normal              |
|                     | PointerOver             |                   | PointerOver         |
|                     | Pressed                 |                   | Pressed             |
|                     | PointerOverPressed      |                   | [利用不可]       |
|                     | Disabled                |                   | [利用不可]       |
|                     | [利用不可]           |                   | PointerOverSelected |
|                     | [利用不可]           |                   | Selected            |
|                     | [利用不可]           |                   | PressedSelected     |
| [利用不可]       |                         | DisabledStates    |                     |
|                     | [利用不可]           |                   | Disabled            |
|                     | [利用不可]           |                   | Enabled             |
| SelectionHintStates |                         | [利用不可]     |                     |
|                     | VerticalSelectionHint   |                   | [利用不可]       |
|                     | HorizontalSelectionHint |                   | [利用不可]       |
|                     | NoSelectionHint         |                   | [利用不可]       |
| [利用不可]       |                         | MultiSelectStates |                     |
|                     | [利用不可]           |                   | MultiSelectDisabled |
|                     | [利用不可]           |                   | MultiSelectEnabled  |
| SelectionStates     |                         | [利用不可]     |                     |
|                     | Unselecting             |                   | [利用不可]       |
|                     | Unselected              |                   | [利用不可]       |
|                     | UnselectedPointerOver   |                   | [利用不可]       |
|                     | UnselectedSwiping       |                   | [利用不可]       |
|                     | Selecting               |                   | [利用不可]       |
|                     | Selected                |                   | [利用不可]       |
|                     | SelectedSwiping         |                   | [利用不可]       |
|                     | SelectedUnfocused       |                   | [利用不可]       |

カスタムの [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/br242919) コントロール テンプレートまたは [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/hh738501) コントロール テンプレートを使っている場合は、上記の変更を踏まえてテンプレートを確認してください。 新しい既定のテンプレートのコピーを編集し、そのコピーにカスタマイズを再適用して、コントロール テンプレートのカスタマイズをやり直すことをお勧めします。 こうした作業をできない理由があり、既にあるテンプレートの編集が必要になる場合は、既存のテンプレートを編集する方法に関する次の一般的なガイダンスを参考にしてください。

-   新しい MultiSelectStates 表示状態グループを追加します。
-   新しい MultiSelectDisabled 表示状態を追加します。
-   新しい MultiSelectEnabled 表示状態を追加します。
-   新しい DisabledStates 表示状態グループを追加します。
-   新しい Enabled 表示状態を追加します。
-   CommonStates 表示状態グループから PointerOverPressed 表示状態を削除します。
-   Disabled 表示状態を DisabledStates 表示状態グループに移動します。
-   新しい PointerOverSelected 表示状態を追加します。
-   新しい PressedSelected 表示状態を追加します。
-   SelectedHintStates 表示状態グループを削除します。
-   SelectionStates 表示状態グループの Selected 表示状態を CommonStates 表示状態グループに移動します。
-   SelectionStates 表示状態グループ全体を削除します。

## <a name="localization-and-globalization"></a>ローカリゼーションとグローバリゼーション

UWP アプリ プロジェクトで、ユニバーサル 8.1 プロジェクトの Resources.resw ファイルを再利用できます。 このファイルをコピーしてから、プロジェクトに追加し、**[ビルド アクション]** を **[PRIResource]** に、**[出力ディレクトリにコピー]** を **[コピーしない]** に設定します。 「[**ResourceContext.QualifierValues**](https://msdn.microsoft.com/library/windows/apps/br206071)」トピックでは、デバイス ファミリのリソースを選ぶ要因に基づいてデバイス ファミリ固有のリソースを読み込む方法について説明しています。

## <a name="play-to"></a>リモート再生

[**Windows.Media.PlayTo**](https://msdn.microsoft.com/library/windows/apps/br207025) 名前空間の API は Windows 10 アプリでは使われなくなり、[**Windows.Media.Casting**](https://msdn.microsoft.com/library/windows/apps/dn972568) API が使われるようになりました。

## <a name="resource-keys-and-textblock-style-sizes"></a>リソース キー、および TextBlock スタイル サイズ

デザイン言語は Windows 10 で改善され、特定のシステム スタイルが変更されました。 場合によっては、変更されたスタイルのプロパティにビューが適合するように、ビューのビジュアル デザインに戻る必要があります。

それ以外の場合、リソース キーはサポートされなくなりました。 Visual Studio の XAML マークアップ エディターでは、解決できないリソース キーへの参照が強調表示されます。 たとえば XAML マークアップ エディターでは、スタイル キー `ListViewItemTextBlockStyle` への参照の下に赤い波線が引かれます。 これを修正しない場合、エミュレーターかデバイスに展開しようとしたときにアプリが直ちに終了します。 したがって、XAML マークアップの正確性に関する作業に着手することが重要です。 また、そのような問題を検出するために Visual Studio が優れたツールであることがわかります。

まだサポートされているキーに関して、デザイン言語の変更は、一部のスタイルによって設定されるプロパティが変更されたことを意味します。 たとえば、`TitleTextBlockStyle` は **FontSize** を Windows ストア アプリでは 14.667 px に設定し、Windows Phone ストア アプリでは 18.14 px に設定します。 ただし、同じスタイルで、**FontSize** が Windows 10 アプリではかなり大きい 24 px に設定されます。 デザインとレイアウトを確認し、適切なスタイルを適切な場所で使ってください。 詳しくは、「[フォントのガイドライン](https://msdn.microsoft.com/library/windows/apps/hh700394.aspx)」と「[UWP アプリの設計](http://dev.windows.com/design)」をご覧ください。

サポートされなくなったキーの完全な一覧を次に示します。

-   CheckBoxAndRadioButtonMinWidthSize
-   CheckBoxAndRadioButtonTextPaddingThickness
-   ComboBoxFlyoutListPlaceholderTextOpacity
-   ComboBoxFlyoutListPlaceholderTextThemeMargin
-   ComboBoxHighlightedBackgroundThemeBrush
-   ComboBoxHighlightedBorderThemeBrush
-   ComboBoxHighlightedForegroundThemeBrush
-   ComboBoxInlinePlaceholderTextForegroundThemeBrush
-   ComboBoxInlinePlaceholderTextThemeFontWeight
-   ComboBoxItemDisabledThemeOpacity
-   ComboBoxItemHighContrastBackgroundThemeMargin
-   ComboBoxItemMinHeightThemeSize
-   ComboBoxPlaceholderTextBlockStyle
-   ComboBoxPlaceholderTextThemeMargin
-   CommandBarBackgroundThemeBrush
-   CommandBarForegroundThemeBrush
-   ContentDialogButton1HostPadding
-   ContentDialogButton2HostPadding
-   ContentDialogButtonsMinHeight
-   ContentDialogContentLandscapeWidth
-   ContentDialogContentMinHeight
-   ContentDialogDimmingColor
-   ContentDialogTitleMinHeight
-   ControlContextualInfoTextBlockStyle
-   ControlHeaderContentPresenterStyle
-   ControlHeaderTextBlockStyle
-   FlyoutContentPanelLandscapeThemeMargin
-   FlyoutContentPanelPortraitThemeMargin
-   GrabberMargin
-   GridViewItemMargin
-   GridViewItemPlaceholderBackgroundThemeBrush
-   GroupHeaderTextBlockStyle
-   HeaderContentPresenterStyle
-   HighContrastBlack
-   HighContrastWhite
-   HubHeaderCharacterSpacing
-   HubHeaderFontSize
-   HubHeaderMarginThickness
-   HubSectionHeaderCharacterSpacing
-   HubSectionHeaderFontSize
-   HubSectionHeaderMarginThickness
-   HubSectionMarginThickness
-   InlineWindowPlayPauseMargin
-   ItemTemplate
-   LeftFullWindowMargin
-   LeftMargin
-   ListViewEmptyStaticTextBlockStyle
-   ListViewItemContentTextBlockStyle
-   ListViewItemContentTranslateX
-   ListViewItemMargin
-   ListViewItemMultiselectCheckBoxMargin
-   ListViewItemSubheaderTextBlockStyle
-   ListViewItemTextBlockStyle
-   MediaControlPanelAudioThemeBrush
-   MediaControlPanelPhoneVideoThemeBrush
-   MediaControlPanelVideoThemeBrush
-   MediaControlPanelVideoThemeColor
-   MediaControlPlayPauseThemeBrush
-   MediaControlTimeRowThemeBrush
-   MediaControlTimeRowThemeColor
-   MediaDownloadProgressIndicatorThemeBrush
-   MediaErrorBackgroundThemeBrush
-   MediaTextThemeBrush
-   MenuFlyoutBackgroundThemeBrush
-   MenuFlyoutBorderThemeBrush
-   MenuFlyoutLandscapeThemePadding
-   MenuFlyoutLeftLandscapeBorderThemeThickness
-   MenuFlyoutPortraitBorderThemeThickness
-   MenuFlyoutPortraitThemePadding
-   MenuFlyoutRightLandscapeBorderThemeThickness
-   MessageDialogContentStyle
-   MessageDialogTitleStyle
-   MinimalWindowMargin
-   PasswordBoxCheckBoxThemeMargin
-   PhoneAccentBrush
-   PhoneBackgroundBrush
-   PhoneBackgroundColor
-   PhoneBaseBlackColor
-   PhoneBaseHighColor
-   PhoneBaseLowColor
-   PhoneBaseLowSolidColor
-   PhoneBaseMediumHighColor
-   PhoneBaseMediumMidColor
-   PhoneBaseMediumMidSolidColor
-   PhoneBaseMidColor
-   PhoneBaseWhiteColor
-   PhoneBorderThickness
-   PhoneButtonBasePressedForegroundBrush
-   PhoneButtonContentPadding
-   PhoneButtonFontWeight
-   PhoneButtonMinHeight
-   PhoneButtonMinWidth
-   PhoneChromeBrush
-   PhoneChromeColor
-   PhoneControlBackgroundColor
-   PhoneControlDisabledColor
-   PhoneControlForegroundColor
-   PhoneDisabledBrush
-   PhoneDisabledColor
-   PhoneFontFamilyLight
-   PhoneFontFamilySemiBold
-   PhoneForegroundBrush
-   PhoneForegroundColor
-   PhoneHighContrastSelectedBackgroundThemeBrush
-   PhoneHighContrastSelectedForegroundThemeBrush
-   PhoneImagePlaceholderColor
-   PhoneLowBrush
-   PhoneMidBrush
-   PhonePageBackgroundColor
-   PhonePivotLockedTranslation
-   PhonePivotUnselectedItemOpacity
-   PhoneRadioCheckBoxBorderBrush
-   PhoneRadioCheckBoxBrush
-   PhoneRadioCheckBoxCheckBrush
-   PhoneRadioCheckBoxPressedBrush
-   PhoneStrokeThickness
-   PhoneTextHighColor
-   PhoneTextLowColor
-   PhoneTextMidColor
-   PhoneTextOverAccentColor
-   PhoneTouchTargetLargeOverhang
-   PhoneTouchTargetOverhang
-   PivotHeaderItemPadding
-   PlaceholderContentPresenterStyle
-   ProgressBarHighContrastAccentBarThemeBrush
-   ProgressBarIndeterminateRectagleThemeSize
-   ProgressBarRectangleStyle
-   ProgressRingActiveBackgroundOpacity
-   ProgressRingElipseThemeMargin
-   ProgressRingElipseThemeSize
-   ProgressRingTextForegroundThemeBrush
-   ProgressRingTextThemeMargin
-   ProgressRingThemeSize
-   RichEditBoxTextThemeMargin
-   RightFullWindowMargin
-   RightMargin
-   ScrollBarMinThemeHeight
-   ScrollBarMinThemeWidth
-   ScrollBarPanningThumbThemeHeight
-   ScrollBarPanningThumbThemeWidth
-   SliderThumbDisabledBorderThemeBrush
-   SliderTrackBorderThemeBrush
-   SliderTrackDisabledBorderThemeBrush
-   TextBoxBackgroundColor
-   TextBoxBorderColor
-   TextBoxDisabledHeaderForegroundThemeBrush
-   TextBoxFocusedBackgroundThemeBrush
-   TextBoxForegroundColor
-   TextBoxPlaceholderColor
-   TextControlHeaderMarginThemeThickness
-   TextControlHeaderMinHeightSize
-   TextStyleExtraExtraLargeFontSize
-   TextStyleExtraLargePlusFontSize
-   TextStyleMediumFontSize
-   TextStyleSmallFontSize
-   TimeRemainingElementMargin

## <a name="searchbox-deprecated-in-favor-of-autosuggestbox"></a>SearchBox に代わって使われる AutoSuggestBox

ユニバーサル デバイス ファミリでは [**SearchBox**](https://msdn.microsoft.com/library/windows/apps/dn252803) が実装されていますが、モバイル デバイスでは部分的に機能しません。 ユニバーサル検索エクスペリエンスには [**AutoSuggestBox**](https://msdn.microsoft.com/library/windows/apps/dn633874) を使います。 **AutoSuggestBox** を使った検索エクスペリエンスの通常の実装方法を次に示します。

入力を開始すると、**UserInput** という理由で **TextChanged** イベントが発生します。 次に、候補の一覧を設定し、[**AutoSuggestBox**](https://msdn.microsoft.com/library/windows/apps/dn633874) の **ItemsSource** を設定します。 ユーザーが一覧を移動すると、**SuggestionChosen** イベントが発生します (また、**TextMemberDisplayPath** を設定すると、指定されたプロパティに基づいてテキスト ボックスが自動的に入力されます)。 ユーザーが Enter キーで選択項目を送信すると、**QuerySubmitted** イベントが発生し、その時点で、選ばれた候補に対するアクションを実行できます (多くの場合、指定されたコンテンツの詳細を示す別のページに移動するというアクションが実行されます)。 **SearchBoxQuerySubmittedEventArgs** の **LinguisticDetails** プロパティと **Language** プロパティはサポートされなくなった点に注意してください (その機能をサポートする同等の API はあります)。 また、**KeyModifiers** もサポートされなくなりました。

[**AutoSuggestBox**](https://msdn.microsoft.com/library/windows/apps/dn633874) は入力方式エディター (IME) もサポートしています。 必要に応じて "検索" アイコンを表示できます (このアイコンを操作すると **QuerySubmitted** イベントが発生します)。

```xml
   <AutoSuggestBox ... >
        <AutoSuggestBox.QueryIcon>
            <SymbolIcon Symbol="Find"/>
        </AutoSuggestBox.QueryIcon>
    </AutoSuggestBox>
```

[AutoSuggestBox の移植のサンプル](http://go.microsoft.com/fwlink/p/?linkid=619996)に関するページもご覧ください。

## <a name="semanticzoom-changes"></a>SemanticZoom に関する変更

[**SemanticZoom**](https://msdn.microsoft.com/library/windows/apps/hh702601) での縮小表示のジェスチャは、Windows Phone モデルで収束され、グループ ヘッダーをタップまたはクリックするという動作になりました (このため、デスクトップ コンピューターでは、縮小表示するためのマイナス記号のボタンのアフォーダンスは表示されなくなります)。 これで、デバイスに関係なく同一で一貫性のある動作を実現できます。 Windows Phone モデルと外観面で異なる点の 1 つは、縮小表示ビュー (ジャンプ リスト) が、拡大表示ビューをオーバーレイするのではなく、拡大表示ビューを置き換えることです。 このため、縮小表示ビューから半透明の背景を削除することができます。

Windows Phone ストア アプリでは、縮小表示ビューが画面のサイズに合わせて拡張されます。 Windows ストア アプリと Windows 10 アプリでは、縮小表示ビューのサイズは、**SemanticZoom** コントロールの境界に制限されます。

Windows Phone ストア アプリでは、縮小表示ビューで背景に透明度が設定されている場合、そのビューの (Z オーダーに基づく) 背後のコンテンツが透けて表示されます。 Windows ストア アプリと Windows 10 アプリでは、縮小表示ビューの背後に何も表示されません。

Windows ストア アプリでは、アプリが非アクティブ化して再アクティブ化されると、縮小表示ビューが終了し (表示されていた場合)、拡大表示ビューが代わりに表示されます。 Windows Phone ストア アプリと Windows 10 アプリでは、表示されていた場合、拡大表示ビューが表示されたままになります。

Windows Phone ストア アプリと Windows 10 アプリでは、[戻る] ボタンを押すと、縮小表示ビューが閉じられます。 Windows ストア アプリの場合、組み込みの [戻る] ボタンの処理はないため、該当しません。

## <a name="settings"></a>設定

Windows ランタイム 8.x の **SettingsPane** クラスは、Windows 10 には適していません。 代わりに、[設定] ページを作成し、さらに、アプリ内から [設定] ページにアクセスする方法をユーザーに提供する必要があります。 このアプリの [設定] ページはトップ レベルで表示されるようにすることをお勧めしますが、ナビゲーション ウィンドウの最後のピン留めされた項目として、ここにはオプションの完全なセットがあります。

-   ナビゲーション ウィンドウ。 設定は選択肢のナビゲーション リストの最後の項目であり、下部にピン留めしている必要があります。
-   アプリ バー/ツール バー (タブ ビューまたはピボット レイアウト内)。 [設定] はアプリ バーやツール バーのメニューのポップアップで最後の項目であることが必要です。 [設定] をナビゲーション内のトップレベルのいずれかの項目にすることはお勧めしません。
-   ハブ。 [設定] はメニューのポップアップ内に配置する必要があります (ハブ レイアウトでのアプリ バー メニューやツール バー メニューからのポップアップ内など)。

また、マスター詳細ウィンドウ内に [設定] を配置することはお勧めしません。

[設定] ページはアプリのウィンドウ全体を埋め、このページには [バージョン情報] と [フィードバック] があることも必要です。 [設定] ページのデザインに関するガイダンスについては、「[アプリ設定のガイドライン](https://msdn.microsoft.com/library/windows/apps/hh770544)」をご覧ください。

## <a name="text"></a>テキスト

テキスト (または文字体裁) は UWP アプリの重要な要素です。移植するときには、ビューの視覚的なデザインが新しいデザイン言語に適合するように、ビューの視覚的なデザインを再検討することが必要になる場合があります。 次の図を使って、ユニバーサル Windows プラットフォーム (UWP) の利用可能な  **TextBlock** システム スタイルを見つけてください。 使用した Windows Phone Silverlight スタイルに対応するものを見つけます。 また、独自のユニバーサル スタイルを作成し、Windows Phone Silverlight システム スタイルからプロパティをコピーすることもできます。

![Windows10 アプリのシステム TextBlock スタイル](images/label-uwp10stylegallery.png) <br/>Windows 10 アプリのシステム TextBlock スタイル

Windows ストア アプリと Windows Phone ストア アプリでは、既定のフォント ファミリは Global User Interface です。 Windows 10 アプリでは、既定のフォント ファミリは Segoe UI です。 この結果、アプリでのフォント メトリックの表示が異なる可能性があります。 8.1 のテキストの外観を再現する場合は、[**LineHeight**](https://msdn.microsoft.com/library/windows/apps/br209671) や [**LineStackingStrategy**](https://msdn.microsoft.com/library/windows/apps/br244362) などのプロパティを使って、独自のメトリックを設定できます。

Windows ストア アプリと Windows Phone ストア アプリでは、テキストの既定の言語が、ビルドの言語または en-us に設定されます。 Windows 10 アプリでは、Windows の既定の言語はアプリの最上位の言語 (フォント フォールバック) に設定されます。 [**FrameworkElement.Language**](https://msdn.microsoft.com/library/windows/apps/hh702066) を明示的に設定できますが、そのプロパティの値を設定しないと、フォントの優れたフォールバック動作を得ることができます。

詳しくは、「[フォントのガイドライン](https://msdn.microsoft.com/library/windows/apps/hh700394.aspx)」と「[UWP アプリの設計](http://go.microsoft.com/fwlink/p/?LinkID=533896)」をご覧ください。 テキスト コントロールの変更については、上記の「[コントロール](#controls-and-control-styles-and-templates)」セクションもご覧ください。

## <a name="theme-changes"></a>テーマの変更

ユニバーサル 8.1 アプリでは、既定のテーマは濃色になっています。 Windows 10 デバイスでは既定のテーマが変更されていますが、App.xaml で要求するテーマを宣言して、使用するテーマを制御できます。 たとえば、すべてのデバイスで濃色テーマを使うには、`RequestedTheme="Dark"` をルートの Application 要素に追加します。

## <a name="tiles-and-toasts"></a>タイルとトースト

タイルとトーストについては、現在使っているテンプレートを引き続き Windows 10 アプリで使うことができます。 ただし、新しいアダプティブ テンプレートを使うことができ、それらについては「 [通知、タイル、トースト、バッジ](https://msdn.microsoft.com/library/windows/apps/mt185606)」で説明されています。

以前、デスクトップ コンピューターでは、トースト通知は一時的なメッセージでした。 表示が消えるため、見逃したり無視した場合はもう取得できなくなりました。 Windows phone では、トースト通知が無視されたり一時的に消された場合、通知はアクション センターに移されます。 アクション センターは、モバイル デバイス ファミリに限定されたものではなくなりました。

トースト通知を送るために機能を宣言する必要はなくなりました。

## <a name="window-size"></a>ウィンドウ サイズ

ユニバーサル 8.1 アプリでは、アプリ マニフェストの要素 [**ApplicationView**](https://msdn.microsoft.com/library/windows/apps/dn391667) を使って、ウィンドウの最小幅が宣言されます。 UWP アプリでは、命令型コードを使って最小サイズ (幅と高さ) を指定できます。 既定の最小サイズは 500 x 320 epx で、このサイズは受け入れられる最も小さいサイズでもあります。 受け入れられる最も大きいサイズは 500 x 500 epx です。

```csharp
   Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetPreferredMinSize
        (new Size { Width = 500, Height = 500 });
```

次のトピックは、「 [入出力、デバイス、アプリ モデルの移植](w8x-to-uwp-input-and-sensors.md)」です。
