---
title: Azure Cosmos DB 用の Cassandra のキースペースとテーブルを保護するリソース ロックを作成する
description: Azure Cosmos DB 用の Cassandra のキースペースとテーブルを保護するリソース ロックを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 5683eece3f6dc1c63be27ce3c98664e972b8d7c6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086746"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Azure CLI を使用して Azure Cosmos Cassandra API のキースペースとテーブルを保護するリソース ロックを作成する

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択する場合、このトピックでは、Azure CLI バージョン 2.6.0 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

> [!IMPORTANT]
> 先に `disableKeyBasedMetadataWriteAccess` プロパティを有効にして Cosmos DB アカウントをロックしておかないと、ユーザーが Cassandra SDK、CQL Shell、または Azure portal を使って行った変更に対してリソース ロックが機能しません。 このプロパティを有効にする方法の詳細については、[SDK からの変更の防止](../../../role-based-access-control.md#prevent-sdk-changes)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | ロックを作成します。 |
| [az lock list](/cli/azure/lock#az-lock-list) | ロック情報を一覧表示します。 |
| [az lock show](/cli/azure/lock#az-lock-show) | ロックのプロパティを表示します。 |
| [az lock delete](/cli/azure/lock#az-lock-delete) | ロックを削除します。 |

## <a name="next-steps"></a>次のステップ

-[リソースのロックによる予期せぬ変更の防止](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)

-[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
