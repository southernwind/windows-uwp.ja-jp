---
author: mijacobs
description: 
title: "ユーザー画像コントロール"
template: detail.hbs
label: Parallax View
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10、UWP"
pm-contact: kevjey
design-contact: kimsea
dev-contact: kefodero
doc-status: Published
ms.openlocfilehash: c71fdf3d19c8c8e43666620df53258825c4a8eb1
ms.sourcegitcommit: 10d6736a0827fe813c3c6e8d26d67b20ff110f6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2017
---
# <a name="person-picture-control"></a>ユーザー画像コントロール

> [!IMPORTANT]
> この記事では、まだリリースされていない機能について説明しています。この機能は、正式版がリリースされるまでに大幅に変更される可能性があります。 ここに記載された情報について、Microsoft は明示または黙示を問わずいかなる保証をするものでもありません。

ユーザー画像コントロールは、ユーザー画像を利用できる場合はユーザーのアバター画像を表示します。利用できない場合は、ユーザーの頭文字か汎用アイコンを表示します。 このコントロールを使うと、ユーザーの連絡先情報を管理するオブジェクトである [Contact オブジェクト](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact)を表示できます。また、表示名やプロフィール画像などの連絡先情報は手動で提供することもできます。  

> **重要な API**: [PersonPicture クラス](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.personpicture)、[Contact クラス](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact)、[ContactManager クラス](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.ContactManager)

![ユーザー画像コントロール](images/person-picture/person-picture_hero.png)

## <a name="is-this-the-right-control"></a>適切なコントロールの選択

ユーザーと連絡先情報を表示するときはユーザー画像を使います。 このコントロールの使用例をいくつか紹介します。
* 現在のユーザーを表示する
* アドレス帳の連絡先を表示する
* メッセージの送信者を表示する 
* ソーシャル メディアの連絡先を表示する

この図では、連絡先リストのユーザー画像コントロールを示しています。![ユーザー画像コントロール](images/person-picture/person-picture-control.png)

## <a name="how-to-use-the-person-picture-control"></a>ユーザー画像コントロールの使用方法

ユーザー画像を作成するには、PersonPicture クラスを使用します。 この例では、PersonPicture コントロールを作成し、ユーザーの表示名、プロフィール画像、および頭文字を手動で提供します。

```xaml
<Page
    x:Class="App2.ExamplePage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App2"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <StackPanel Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <PersonPicture
            DisplayName="Betsy Sherman"
            ProfilePicture="Assets\BetsyShermanProfile.png"
            Initials="BS" />
    </StackPanel>
</Page>
```

## <a name="using-the-person-picture-control-to-display-a-contact-object"></a>ユーザー画像コントロールを使用して、Contact オブジェクトを表示する

ユーザー選択コントロールを使用して、[Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact) オブジェクトを表示できます。 

```xaml
<Page
    x:Class="SampleApp.PersonPictureContactExample"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:SampleApp"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <StackPanel Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <PersonPicture
            Contact="{x:Bind CurrentContact, Mode=OneWay}" />
            
        <Button Click="LoadContactButton_Click">Load contact</Button>
    </StackPanel>
</Page>
```

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;
using Windows.ApplicationModel.Contacts;

namespace SampleApp
{
    public sealed partial class PersonPictureContactExample : Page, System.ComponentModel.INotifyPropertyChanged
    {
        public PersonPictureContactExample()
        {
            this.InitializeComponent();
        }

        private Windows.ApplicationModel.Contacts.Contact _currentContact; 
        public Windows.ApplicationModel.Contacts.Contact CurrentContact
        {
            get => _currentContact;
            set
            {
                _currentContact = value;
                PropertyChanged?.Invoke(this,
                    new System.ComponentModel.PropertyChangedEventArgs(nameof(CurrentContact)));
            }

        }
        public event System.ComponentModel.PropertyChangedEventHandler PropertyChanged;

        public static async System.Threading.Tasks.Task<Windows.ApplicationModel.Contacts.Contact> CreateContact()
        {

            var contact = new Windows.ApplicationModel.Contacts.Contact();
            contact.FirstName = "Betsy";
            contact.LastName = "Sherman";

            // Get the app folder where the images are stored.
            var appInstalledFolder = 
                Windows.ApplicationModel.Package.Current.InstalledLocation;
            var assets = await appInstalledFolder.GetFolderAsync("Assets");
            var imageFile = await assets.GetFileAsync("betsy.png");
            contact.SourceDisplayPicture = imageFile;

            return contact;
        }

        private async void LoadContactButton_Click(object sender, RoutedEventArgs e)
        {
            CurrentContact = await CreateContact();
        }
    }
}
```

> [!NOTE]
> コードを簡潔にするために、この例では新しい Contact オブジェクトを作成しています。 実際のアプリでは、ユーザーに連絡先を選択してもらうか、[ContactManager](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.ContactManager) を使用して連絡先リストを照会します。 連絡先の取得と管理については、[連絡先とカレンダーの記事](../contacts-and-calendar/index.md)をご覧ください。 

## <a name="determining-which-info-to-display"></a>表示する情報の決定

[Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact) オブジェクトを提供すると、ユーザー画像コントロールによってそのオブジェクトが評価され、表示できる情報が判断されます。 

画像を利用できる場合、コントロールでは次の優先順位で最初に見つかった画像を表示します。

1.    LargeDisplayPicture
2.    SmallDisplayPicture
3.    Thumbnail

選択される画像は、PreferSmallImage プロパティを true に設定することで変更できます。このように設定すると、SmallDisplayPicture の優先順位が LargeDisplayPicture よりも高くなります。

画像がない場合、コントロールは連絡先の名前か頭文字を表示します。名前のデータがない場合、コントロールはメール アドレスや電話番号などの連絡先データを表示します。 

## <a name="related-articles"></a>関連記事

* [連絡先とカレンダー](../contacts-and-calendar/index.md)
* [連絡先カードのサンプル](http://go.microsoft.com/fwlink/p/?LinkId=624040)



