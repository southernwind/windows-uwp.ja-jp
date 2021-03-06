---
title: ストリーミング リソースに利用可能なアドレス空間
description: このセクションでは、リソースのストリーミングに利用できる仮想アドレス空間を指定します。
ms.assetid: 145EB4A3-3910-4126-BC7E-A4CF53E2A098
keywords:
- ストリーミング リソースに利用可能なアドレス空間
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 35a591d805870df97ee03169b20e664316e094a7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57652377"
---
# <a name="address-space-available-for-streaming-resources"></a>ストリーミング リソースに利用可能なアドレス空間


このセクションでは、リソースのストリーミングに利用できる仮想アドレス空間を指定します。

64 ビット オペレーティング システムでは、少なくとも 40 ビットの仮想アドレス空間 (1テラバイト) が利用可能です。

32 ビット オペレーティング システムの場合、アドレス空間は 32 ビット (4 GB) です。 32 ビット ARM システムでは、27 ビットを超えるアドレス空間 (128 MB) を割り当てで使用すると、個々のストリーミング リソースの作成が失敗する可能性があります。 これには、ミップマップ、パッキングされたタイル パディング、または 2 の累乗のパディング サーフェス ディメンションなどのために、ハードウェアが使用する可能性がある、アドレス空間内の隠れたパディングが含まれます。

グラフィックス処理装置 (GPU) のための別個のページ テーブルを持つグラフィックス システムでは、このアドレス空間の大部分はアプリケーションによって作られた GPU リソースに対して利用可能となりますが、ディスプレイ ドライバーによって行われる GPU 割り当ては同じ空間に収まります。

CPU と GPU の間で共有されるページ テーブルを持つ将来のシステムでは、使用可能なアドレス空間は、プロセス内のすべての CPU と GPU 割り当ての間で共有されます。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>関連トピック


[リソースの作成パラメーターのストリーミング](streaming-resource-creation-parameters.md)

 

 




