---
description: このトピックでは、イベントを発生させるランタイム クラスを含む、Windows ランタイム コンポーネントを作成する方法を示します。 コンポーネントを使用してイベントを処理するアプリも示します。
title: C++/WinRT でのイベントの作成
ms.date: 04/23/2019
ms.topic: article
keywords: windows 10, uwp, 標準, c++, cpp, winrt, プロジェクション, 作成者, イベント
ms.localizationpriority: medium
ms.openlocfilehash: e7cb0e10efec9077292faa8f8d72a7dad1da2c1b
ms.sourcegitcommit: 83f4a528b5e3fc2b140c76fdf2acf734d6d851d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2020
ms.locfileid: "84683381"
---
# <a name="author-events-in-cwinrt"></a>C++/WinRT でのイベントの作成

このトピックでは、銀行口座 (その残高が借方にに入るときにイベントを発生させる口座) を表すランタイム クラスを含む Windows ランタイム コンポーネントを作成する方法を示します。 このトピックでは、銀行口座ランタイム クラスを使用し、関数を呼び出して残高を調整して、発生するイベントを処理するコア アプリも示します。

> [!NOTE]
> [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) Visual Studio Extension (VSIX) と NuGet パッケージ (両者が連携してプロジェクト テンプレートとビルドをサポート) のインストールと使用については、[Visual Studio での C++/WinRT のサポート](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)に関する記事を参照してください。

> [!IMPORTANT]
> C++/WinRT でランタイム クラスを使用および作成する方法についての理解をサポートするために重要な概念と用語については、「[C++/WinRT での API の使用](consume-apis.md)」と「[C++/WinRT での作成者 API](author-apis.md)」を参照してください。

## <a name="create-a-windows-runtime-component-bankaccountwrc"></a>Windows ランタイム コンポーネントの作成 (BankAccountWRC)

まず、Microsoft Visual Studio で、新しいプロジェクトを作成します。 **Windows ランタイム コンポーネント (C++/WinRT)** プロジェクトを作成し、("銀行口座 Windows ランタイム コンポーネント" 用に) *BankAccountWRC* と名前を付けます。 プロジェクトに *BankAccountWRC* という名前を付けると、このトピックの残りの手順が非常に簡単になります。 プロジェクトはまだビルドしないでください。

新しく作成したプロジェクトには、`Class.idl` という名前のファイルが含まれています。 ソリューション エクスプローラーで、そのファイル `BankAccount.idl` の名前を変更します (`.idl` ファイルの名前を変更すると、依存ファイル `.h` および `.cpp` も自動的に名前変更されます)。 `BankAccount.idl` の内容を、以下のリストで置き換えます。

```idl
// BankAccountWRC.idl
namespace BankAccountWRC
{
    runtimeclass BankAccount
    {
        BankAccount();
        event Windows.Foundation.EventHandler<Single> AccountIsInDebit;
        void AdjustBalance(Single value);
    };
}
```

ファイルを保存します。 現時点ではプロジェクトは完成するまでビルドされませんが、ここでビルドすることは有用です。それによって、**BankAccount** ランタイム クラスを実装するためのソース コード ファイルが生成されるからです。 それでは、今すぐビルドしてみましょう (この段階で表示されることが予想されるビルド エラーは、`Class.h` と `Class.g.h` が見つからないことに関係しています)。

ビルド プロセス中に、`midl.exe` ツールが実行されてコンポーネントの Windows ランタイム メタデータ ファイルが作成されます (これは `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd` です)。 次に、`cppwinrt.exe` ツールが (`-component` オプションで) 実行され、コンポーネントの作成をサポートするソース コード ファイルが生成されます。 これらのファイルには、IDL で宣言した **BankAccount** ランタイム クラスの実装を開始するためのスタブが含まれています。 これらのスタブは `\BankAccountWRC\BankAccountWRC\Generated Files\sources\BankAccount.h` と `BankAccount.cpp` です。

プロジェクト ノードを右クリックし、 **[エクスプローラーでフォルダーを開く]** をクリックします。 これにより、エクスプローラーでプロジェクト フォルダーが開きます。 そこで、スタブ ファイル `BankAccount.h` および `BankAccount.cpp` をフォルダー `\BankAccountWRC\BankAccountWRC\Generated Files\sources\` から、ご自分のプロジェクト ファイルを含むフォルダー `\BankAccountWRC\BankAccountWRC\` にコピーし、コピー先のファイルを置き換えます。 ここで、`BankAccount.h` と `BankAccount.cpp` を開いてランタイム クラスを実装してみましょう。 `BankAccount.h` で、**BankAccount** の実装 (ファクトリの実装*ではありません*) に 2 つのプライベート メンバーを追加します。

```cppwinrt
// BankAccount.h
...
namespace winrt::BankAccountWRC::implementation
{
    struct BankAccount : BankAccountT<BankAccount>
    {
        ...

    private:
        winrt::event<Windows::Foundation::EventHandler<float>> m_accountIsInDebitEvent;
        float m_balance{ 0.f };
    };
}
...
```

上に示したように、イベントは、特定のデリゲート型によってパラメータ化された [**winrt :: event**](/uwp/cpp-ref-for-winrt/event) 構造体テンプレートについて実装されています。

`BankAccount.cpp` で、次のコード例に示すように関数を実装します。 C++/WinRT では、IDL で宣言したイベントは過負荷の状態である関数のセットとして実装されます (プロパティが過負荷の状態の Get および Set 関数のペアとして実装される方法と同様です)。 1 つのオーバーロードが登録するデリゲートを受け取り、トークンを返します。 別のオーバーロードはトークンを受け取り、関連付けられているデリゲートの登録を取り消します。

```cppwinrt
// BankAccount.cpp
...
#include "BankAccountWRC.g.cpp"
namespace winrt::BankAccountWRC::implementation
{
    winrt::event_token BankAccount::AccountIsInDebit(Windows::Foundation::EventHandler<float> const& handler)
    {
        return m_accountIsInDebitEvent.add(handler);
    }

    void BankAccount::AccountIsInDebit(winrt::event_token const& token) noexcept
    {
        m_accountIsInDebitEvent.remove(token);
    }

    void BankAccount::AdjustBalance(float value)
    {
        m_balance += value;
        if (m_balance < 0.f) m_accountIsInDebitEvent(*this, m_balance);
    }
}
```

また、両方のファイルから `static_assert` を削除する必要もあります。

> [!NOTE]
> 自動イベント リボーカーについて詳しくは、「[登録済みデリゲートの取り消し](handle-events.md#revoke-a-registered-delegate)」をご覧ください。 イベント用の自動イベント リボーカーの実装は、無料で手に入ります。 つまり、C++/WinRT プロジェクションによって提供されているイベント リボーカーのオーバーロードを実装する必要はありません。

他のオーバーロード (登録と手動取り消しのオーバーロード) は、プロジェクションには書き込まれて "*いません*"。 それは、シナリオに最適なように柔軟に実装できるようにするためです。 これらの実装で示されているような [**event::add**](/uwp/cpp-ref-for-winrt/event#eventadd-function) および [**event::remove**](/uwp/cpp-ref-for-winrt/event#eventremove-function) の呼び出しは、効率的で同時実行の安全性を確保できるスレッドセーフな既定値です。 ただし、大量のイベントがある場合は、各イベントのイベント フィールドが必要ないことがあり、代わりに、ある種のスパース実装を選択します。

また、**AdjustBalance** 関数の実装によって、残高が負の値になった場合でも **AccountIsInDebit** イベントが発生することを上で確認できます。

任意の警告によってビルドが妨げられる場合は、該当する警告を解決するか、またはプロジェクトのプロパティ **C/C++**  > **General** > **Treat Warnings As Errors** を **No (/WX-)** に設定して、もう一度プロジェクトをビルドします。

## <a name="create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component"></a>コア アプリ (BankAccountCoreApp) を作成して Windows ランタイム コンポーネントをテストする

ここで (*BankAccountWRC* ソリューション、または新しいソリューションのいずれかに) 新しいプロジェクトを作成します。 **コア アプリ (C++/WinRT)** プロジェクトを作成し、*BankAccountCoreApp* と名前を付けます。 2 つのプロジェクトが同じソリューションに属している場合は、*BankAccountCoreApp* をスタートアップ プロジェクトとして設定します。

> [!NOTE]
> 既に説明したように、Windows ランタイム コンポーネント (そのプロジェクト名は *BankAccountWRC*) の Windows ランタイム メタデータ ファイルは `\BankAccountWRC\Debug\BankAccountWRC\` フォルダーに作成されます。 このパスの最初のセグメントは、ソリューション ファイルを含むフォルダーの名前です。次のセグメントは、`Debug` という名前のサブディレクトリです。最後のセグメントは、Windows ランタイム コンポーネントの名前を付けられたサブディレクトリです。 プロジェクトに *BankAccountWRC* という名前を指定しなかった場合、メタデータ ファイルは `\<YourProjectName>\Debug\<YourProjectName>\` フォルダーに配置されます。

次に、コア アプリ プロジェクト (*BankAccountCoreApp*) で、参照を追加し、`\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd` を参照します (または 2 つのプロジェクトが同じソリューションに属している場合は、プロジェクト間の参照を追加します)。 **[追加]** をクリックして **[OK]** をクリックします。 ここで *BankAccountCoreApp* をビルドします。 万が一、ペイロード ファイル `readme.txt` が存在しないというエラーが表示された場合、Windows ランタイム コンポーネント プロジェクトからそのファイルを除外し、これを再ビルドしてから、*BankAccountCoreApp* を再ビルドします。

ビルド プロセス中に、`cppwinrt.exe` ツールが実行され、参照されている `.winmd` ファイルを投影型を含むソース コード ファイルに処理して、コンポーネントの使用をサポートします。 コンポーネントのランタイム クラス (`BankAccountWRC.h` という名前) の投影型のヘッダーは、フォルダー `\BankAccountCoreApp\BankAccountCoreApp\Generated Files\winrt\` 内に生成されます。

そのヘッダーを `App.cpp` に含めます。

```cppwinrt
#include <winrt/BankAccountWRC.h>
```

`App.cpp` でも、(投影型の既定のコンストラクターを使用して) **BankAccount** のインスタンスを作成するために次のコードを追加し、イベント ハンドラーを登録して、口座が借方に入るようにします。

`WINRT_ASSERT` はマクロ定義であり、[_ASSERTE](/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros) に展開されます。

```cppwinrt
struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    BankAccountWRC::BankAccount m_bankAccount;
    winrt::event_token m_eventToken;
    ...
    
    void Initialize(CoreApplicationView const &)
    {
        m_eventToken = m_bankAccount.AccountIsInDebit([](const auto &, float balance)
        {
            WINRT_ASSERT(balance < 0.f); // Put a breakpoint here.
        });
    }
    ...

    void Uninitialize()
    {
        m_bankAccount.AccountIsInDebit(m_eventToken);
    }
    ...

    void OnPointerPressed(IInspectable const &, PointerEventArgs const & args)
    {
        m_bankAccount.AdjustBalance(-1.f);
        ...
    }
    ...
};
```

ウィンドウをクリックするたびに、銀行口座の残高から 1 を減算します。 期待どおりにイベントが発生することを実証するには、**AccountIsInDebit** イベントを処理しているラムダ式内にブレークポイントを置き、アプリを実行して、そのウィンドウ内をクリックします。

## <a name="parameterized-delegates-across-an-abi"></a>ABI 間でのパラメーター化されたデリゲート

ご利用のイベントがアプリケーション バイナリ インターフェイス (ABI) 間で (コンポーネントと処理を行うそのアプリケーションとの間など) アクセス可能である必要がある場合、そのイベントでは Windows ランタイム デリゲート型が使用される必要があります。 上記の例では、[**Windows::Foundation::EventHandler\<T\>** ](/uwp/api/windows.foundation.eventhandler) Windows ランタイム デリゲート型が使用されています。 [**TypedEventHandler\<TSender, TResult\>** ](/uwp/api/windows.foundation.eventhandler) は、Windows ランタイム デリゲート型のもう 1 つの例です。

これら 2 つのデリゲート型用の型パラメーターは ABI をまたがる必要があるため、型パラメーターも Windows ランタイム型にする必要があります。 これには、Windows ランタイム クラス、サードパーティ製のランタイム クラス、数値や文字列などのプリミティブ型が含まれます。 その制約を忘れた場合でも、コンパイラがエラー "*WinRT 型である必要があります*" を表示してサポートしてくれます。

次に、コード リスティングの形式での例を示します。 このトピックの前半で作成した **BankAccountWRC** および **BankAccountCoreApp** プロジェクトから開始し、これらのプロジェクト内でコードを編集して、リスティングのコードのように見えるようにします。

最初に示すこのリスティングは、**BankAccountWRC** プロジェクトのためのものです。 次に示すように `BankAccountWRC.idl` を編集してから、プロジェクトをビルドし、前の手順で `BankAccount.h` および `.cpp` について行ったのと同じようにして、`MyEventArgs.h` および `.cpp` をプロジェクトにコピーします (`Generated Files` フォルダーから)。

```cppwinrt
// BankAccountWRC.idl
namespace BankAccountWRC
{
    [default_interface]
    runtimeclass MyEventArgs
    {
        Single Balance{ get; };
    }

    [default_interface]
    runtimeclass BankAccount
    {
        ...
        event Windows.Foundation.EventHandler<BankAccountWRC.MyEventArgs> AccountIsInDebit;
        ...
    };
}

// MyEventArgs.h
#pragma once
#include "MyEventArgs.g.h"

namespace winrt::BankAccountWRC::implementation
{
    struct MyEventArgs : MyEventArgsT<MyEventArgs>
    {
        MyEventArgs() = default;
        MyEventArgs(float balance);
        float Balance();

    private:
        float m_balance{ 0.f };
    };
}

// MyEventArgs.cpp
#include "pch.h"
#include "MyEventArgs.h"
#include "MyEventArgs.g.cpp"

namespace winrt::BankAccountWRC::implementation
{
    MyEventArgs::MyEventArgs(float balance) : m_balance(balance)
    {
    }

    float MyEventArgs::Balance()
    {
        return m_balance;
    }
}

// BankAccount.h
...
struct BankAccount : BankAccountT<BankAccount>
{
...
    winrt::event_token AccountIsInDebit(Windows::Foundation::EventHandler<BankAccountWRC::MyEventArgs> const& handler);
...
private:
    winrt::event<Windows::Foundation::EventHandler<BankAccountWRC::MyEventArgs>> m_accountIsInDebitEvent;
...
}
...

// BankAccount.cpp
#include "MyEventArgs.h"
...
winrt::event_token BankAccount::AccountIsInDebit(Windows::Foundation::EventHandler<BankAccountWRC::MyEventArgs> const& handler) { ... }
...
void BankAccount::AdjustBalance(float value)
{
    m_balance += value;

    if (m_balance < 0.f)
    {
        auto args = winrt::make_self<winrt::BankAccountWRC::implementation::MyEventArgs>(m_balance);
        m_accountIsInDebitEvent(*this, *args);
    }
}
...
```

このリスティングは、**BankAccountCoreApp** プロジェクトのためのものです。

```cppwinrt
// App.cpp
...
void Initialize(CoreApplicationView const&)
{
    m_eventToken = m_bankAccount.AccountIsInDebit([](const auto&, BankAccountWRC::MyEventArgs args)
    {
        float balance = args.Balance();
        WINRT_ASSERT(balance < 0.f); // Put a breakpoint here.
    });
}
...
```

## <a name="simple-signals-across-an-abi"></a>ABI 間のシンプルなシグナル

イベントにパラメーターや引数を渡す必要がない場合は、独自のシンプルな Windows ランタイム デリゲート型を定義できます。 以下の例では、**BankAccount** ランタイム クラスのよりシンプルなバージョンを示します。 これは、**SignalDelegate** という名前のデリゲート型を宣言してから、それを使用して、パラメーターを持つイベントではなくシグナル型のイベントを発生させます。

```idl
// BankAccountWRC.idl
namespace BankAccountWRC
{
    delegate void SignalDelegate();

    runtimeclass BankAccount
    {
        BankAccount();
        event BankAccountWRC.SignalDelegate SignalAccountIsInDebit;
        void AdjustBalance(Single value);
    };
}
```

```cppwinrt
// BankAccount.h
...
namespace winrt::BankAccountWRC::implementation
{
    struct BankAccount : BankAccountT<BankAccount>
    {
        ...

        winrt::event_token SignalAccountIsInDebit(BankAccountWRC::SignalDelegate const& handler);
        void SignalAccountIsInDebit(winrt::event_token const& token);
        void AdjustBalance(float value);

    private:
        winrt::event<BankAccountWRC::SignalDelegate> m_signal;
        float m_balance{ 0.f };
    };
}
```

```cppwinrt
// BankAccount.cpp
...
namespace winrt::BankAccountWRC::implementation
{
    winrt::event_token BankAccount::SignalAccountIsInDebit(BankAccountWRC::SignalDelegate const& handler)
    {
        return m_signal.add(handler);
    }

    void BankAccount::SignalAccountIsInDebit(winrt::event_token const& token)
    {
        m_signal.remove(token);
    }

    void BankAccount::AdjustBalance(float value)
    {
        m_balance += value;
        if (m_balance < 0.f)
        {
            m_signal();
        }
    }
}
```

```cppwinrt
// App.cpp
struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    BankAccountWRC::BankAccount m_bankAccount;
    winrt::event_token m_eventToken;
    ...
    
    void Initialize(CoreApplicationView const &)
    {
        m_eventToken = m_bankAccount.SignalAccountIsInDebit([] { /* ... */ });
    }
    ...

    void Uninitialize()
    {
        m_bankAccount.SignalAccountIsInDebit(m_eventToken);
    }
    ...

    void OnPointerPressed(IInspectable const &, PointerEventArgs const & args)
    {
        m_bankAccount.AdjustBalance(-1.f);
        ...
    }
    ...
};
```

## <a name="parameterized-delegates-simple-signals-and-callbacks-within-a-project"></a>パラメーター化されたデリゲート、シンプルなシグナル、およびプロジェクト内でのコールバック
Visual Studio プロジェクトの内部の (複数のバイナリにまたがらない) イベントが必要であり、それらのイベントが Windows ランタイム型に限定されない場合でも、[**winrt::event**](/uwp/cpp-ref-for-winrt/event)\<Delegate\> クラス テンプレートを使用できます。 **winrt::delegate** は Windows ランタイム以外のパラメーターもサポートするため、実際の Windows ランタイム デリゲート型の代わりに [**winrt::delegate**](/uwp/cpp-ref-for-winrt/delegate) を使用するだけです。

以下の例では、最初にパラメーターを取らないデリゲート シグネチャ (基本的にシンプルなシグナル) を示し、次に文字列を取るシグネチャを示します。

```cppwinrt
winrt::event<winrt::delegate<>> signal;
signal.add([] { std::wcout << L"Hello, "; });
signal.add([] { std::wcout << L"World!" << std::endl; });
signal();

winrt::event<winrt::delegate<std::wstring>> log;
log.add([](std::wstring const& message) { std::wcout << message.c_str() << std::endl; });
log.add([](std::wstring const& message) { Persist(message); });
log(L"Hello, World!");
```

イベントには、サブスクライブしているデリゲートを必要な数だけ追加できることに注目してください。 ただし、イベントに関連する何らかのオーバーヘッドがあります。 ご自分で必要としているのがサブスクライブしている単一のデリゲートのみを含むシンプルなコールバックのみである場合は、[**winrt::delegate&lt;...T&gt;** ](/uwp/cpp-ref-for-winrt/delegate) だけを使用できます。

```cppwinrt
winrt::delegate<> signalCallback;
signalCallback = [] { std::wcout << L"Hello, World!" << std::endl; };
signalCallback();

winrt::delegate<std::wstring> logCallback;
logCallback = [](std::wstring const& message) { std::wcout << message.c_str() << std::endl; }f;
logCallback(L"Hello, World!");
```

イベントとデリゲートがプロジェクトの内部で使用されている C++/CX コードベースからの移植を行う場合、**winrt::delegate** を使用すると、C++/WinRT でそのパターンをレプリケートできます。

## <a name="design-guidelines"></a>設計ガイドライン

関数パラメーターとしては、デリゲートではなくイベントを渡すことをお勧めします。 [**winrt::event**](/uwp/cpp-ref-for-winrt/event) の **add** 関数は 1 つの例外です。この場合はデリゲートを渡す必要があるからです。 このガイドラインを設けた理由は、デリゲートが (クライアント登録を 1 つしかサポートしていないのか複数サポートしているかの観点から) さまざまな Windows ランタイム言語で異なる形式を取る可能性があるからです。 複数サブスクライバ モデルを使用したイベントでは、より予測可能で一貫性のあるオプションが構成されます。

イベント ハンドラー デリゲート用のシグネチャは、次の 2 つのパラメーターで構成される必要があります: *sender* (**IInspectable**) と *args* (何らかのイベント引数型。たとえば、[**RoutedEventArgs**](/uwp/api/windows.ui.xaml.routedeventargs))。

内部 API を設計している場合は、これらのガイドラインが必ずしも適用されるわけではないことに注意してください。 ただし、内部 API は時間の経過に伴い公開されることがよくあります。

## <a name="related-topics"></a>関連トピック
* [C++/WinRT で API を作成する](author-apis.md)
* [C++/WinRT で API を使用する](consume-apis.md)
* [C++/WinRT でのデリゲートを使用したイベントの処理](handle-events.md)