---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "67181525"
---
### <a name="retrieve-output-files"></a>出力ファイルを取得する

Azure Portal を使用して、ffmpeg タスクによって生成された出力 MP3 ファイルをダウンロードすることができます。 

1. **[すべてのサービス]**  >  **[ストレージ アカウント]** の順にクリックし、ストレージ アカウントの名前をクリックします。
2. **[BLOB]**  >  *[出力]* の順にクリックします。
3. 出力 MP3 ファイルの 1 つを右クリックして、 **[ダウンロード]** をクリックします。 ブラウザーのメッセージに従って、ファイルを開くか保存します。

![出力ファイルをダウンロードする](./media/batch-common-tutorial-download/download.png)

このサンプルには示されていませんが、コンピューティング ノードまたはストレージ コンテナーからプログラムでファイルをダウンロードすることもできます。
