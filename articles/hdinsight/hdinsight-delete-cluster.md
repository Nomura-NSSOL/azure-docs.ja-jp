---
title: HDInsight クラスターを削除する方法 - Azure
description: Azure HDInsight クラスターを削除できるさまざまな方法についての情報
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 60f3ce0ad4f6ee6b1a38130867e0bcd1420620ef
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086366"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する

HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 このドキュメントでは、[Azure portal](https://portal.azure.com)、[Azure PowerShell Az モジュール](https://docs.microsoft.com/powershell/azure/overview)、および [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使ってクラスターを削除する方法について説明します。

> [!IMPORTANT]  
> HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントまたは Data Lake Storage は削除されません。 これらのサービスに格納されているデータは、将来再利用できます。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインする

2. 左側メニューから、 **[すべてのサービス]**  >  **[分析]**  >  **[HDInsight クラスター]** に移動し、クラスターを選択します。

3. 既定のビューから、 **[削除]** アイコンを選択します。 クラスターを削除するプロンプトに従います。

    ![HDInsight クラスター削除ボタン](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

`CLUSTERNAME` を、以下のコード内の HDInsight クラスターの名前に置き換えます。 クラスターを削除するには、PowerShell プロンプトで次のコマンドを実行します。

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME`を HDInsight クラスターの名前、および`RESOURCEGROUP`次のコードでのリソース グループの名前に置き換えます。  クラスターを削除するには、コマンド プロンプトで以下を実行します。

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
