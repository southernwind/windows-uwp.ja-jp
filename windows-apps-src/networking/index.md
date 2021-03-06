---
ms.assetid: 7bb9fd81-8ab5-4f8d-a854-ce285b0669a4
description: ネットワークと Web サービスにアクセスするためのテクノロジ。
title: ネットワークと Web サービス
ms.date: 11/26/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 673f774c4d686726b59ff4e52e356ec0bc319379
ms.sourcegitcommit: f727b68e86a86c94eff00f67ed79a1c12666e7bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "66370763"
---
# <a name="networking-and-web-services"></a>ネットワークと Web サービス

ユニバーサル Windows プラットフォーム (UWP) 開発者は、次のネットワークと Web サービス テクノロジを利用できます。

| トピック | 説明 |
| - | - |
| [ネットワークの基本](networking-basics.md) | ネットワーク対応アプリで実行する必要がある事柄について説明します。 |
| [アプリに適したネットワーク テクノロジ](which-networking-technology.md) | UWP 開発者が利用できるネットワーク テクノロジの概要と、アプリに適したテクノロジを選ぶヒントを説明します。 |
| [バックグラウンドでのネットワーク通信](network-communications-in-the-background.md) | バックグラウンドでないときにネットワーク通信を続けるため、アプリはバックグラウンド タスクを使うことができます。ソケット ブローカーまたはコントロール チャネルがトリガーされます。 |
| [ソケット](sockets.md) | ソケットは、下位レベルのデータ転送テクノロジであり、多くのネットワーク プロトコルがこの上に実装されています。 UWP は、接続が長期間維持されるか、確立された接続が必要あるかどうかに関係なく、クライアント/サーバー アプリケーションまたは ピア ツー ピア アプリケーションの TCP および UDP ソケット クラスを提供します。 |
| [WebSocket](websockets.md) | WebSocket は、クライアントとサーバー間の高速で安全な双方向通信を、HTTP(S) を使った Web 経由で実現するメカニズムを提供し、UTF-8 メッセージとバイナリ メッセージの両方をサポートします。 |
| [HttpClient](httpclient.md) | HTTP 2.0 プロトコルと HTTP 1.1 プロトコルを使って情報を送受信するには、[Windows.Web.Http](https://docs.microsoft.com/uwp/api/Windows.Web.Http) 名前空間 API を使います。 |
| [RSS/Atom フィード](web-feeds.md) | [Windows.Web.Syndication](https://docs.microsoft.com/uwp/api/Windows.Web.Syndication) 名前空間の機能を利用し、RSS や Atom の標準に従って生成される概要フィードを使って、最新の人気の高い Web コンテンツを取得または作成します。 |
| [バックグラウンド転送](background-transfers.md) | ネットワーク経由でファイルを確実にコピーするには、バックグラウンド転送 API を使います。 |
