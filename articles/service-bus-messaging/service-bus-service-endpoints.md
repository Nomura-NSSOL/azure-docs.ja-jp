---
title: Azure Service Bus 用の仮想ネットワーク サービス エンドポイントを構成する
description: この記事では、Microsoft.ServiceBus サービス エンドポイントを仮想ネットワークに追加する方法について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d7f1783f197804e12a8c2d20a0c46b6efd2160
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071317"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Azure Service Bus 用の仮想ネットワーク サービス エンドポイントを構成する

Service Bus と[仮想ネットワーク (VNet) サービス エンドポイント][vnet-sep]の統合により、メッセージング機能へのアクセスを、仮想ネットワークにバインドされている仮想マシンなどのワークロードから保護し、ネットワーク トラフィック パスを両端で保護できます。

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドするように構成した後、それぞれの Service Bus 名前空間は、承認された仮想ネットワークを除くどこからのトラフィックも受け入れなくなります。また、任意で、特定のインターネット IP アドレスからトラフィックを受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Service Bus 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。

その結果、メッセージング サービス エンドポイントの監視可能なネットワーク アドレスがパブリック IP 範囲内にあるにもかかわらず、サブネットにバインドされたワークロードとそれぞれの Service Bus 名前空間の間にプライベートな分離された関係が確立されます。

>[!WARNING]
> 仮想ネットワーク統合を実装すると、他の Azure サービスが Service Bus と対話するのを防ぐことができます。
>
> 仮想ネットワークが実装されているときは、信頼できる Microsoft サービスはサポートされません。
>
> 仮想ネットワークでは動作しない Azure の一般的なシナリオは次のとおりです (網羅的なリストでは**ない**ことに注意してください)
> - Azure Event Grid との統合
> - Azure IoT Hub ルート
> - Azure IoT Device Explorer
>
> 次の Microsoft サービスが仮想ネットワーク上に存在する必要があります
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> 仮想ネットワークは、[Premium レベル](service-bus-premium-messaging.md)の Service Bus 名前空間でのみサポートされます。
> 
> Service Bus で VNet サービス エンドポイントを使用するときには、Standard レベルと Premium レベルの Service Bus 名前空間が混在するアプリケーションでこれらのエンドポイントを有効にしないでください。 これは Standard レベルでは VNet がサポートされないためです。 エンドポイントは、Premium レベルの名前空間のみに制限されます。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet の統合によって有効になる高度なセキュリティのシナリオ 

厳格でコンパートメント化されたセキュリティを要求し、仮想ネットワーク サブネットがコンパートメント化されたサービス間のセグメント化を提供するソリューションでも、一般に、それらのコンパートメント内に存在するサービス間の通信パスが必要です。

TCP/IP 上で HTTPS を搬送するものを含め、コンパートメント間の直接の IP ルートには、ネットワーク レイヤーから上のレイヤーの脆弱性を悪用されるリスクがあります。 メッセージング サービスは完全に隔離された通信パスを提供し、そこではメッセージがパーティ間を移動するときにディスクにも書き込まれます。 両方とも同じ Service Bus インスタンスにバインドされている 2 つの異なる仮想ネットワーク内のワークロードは、それぞれのネットワークの分離境界の整合性を維持しながら、メッセージを介して効率よく確実に通信できます。
 
つまり、セキュリティ対策されたクラウド ソリューションは、信頼性が高くスケーラブルな Azure の業界をリードする非同期メッセージング機能にアクセスできるだけでなく、メッセージングを使用して、セキュリティで保護されたソリューション コンパートメント間に通信パスを作成できます。この通信パスは、HTTPS やその他の TLS で保護されたソケット プロトコルを含むピアツーピア通信モードよりも本質的に安全です。

## <a name="binding-service-bus-to-virtual-networks"></a>Service Bus を仮想ネットワークにバインドする

"*仮想ネットワーク規則*" は、Azure Service Bus サーバーが特定の仮想ネットワーク サブネットからの接続を許可するかどうかを制御するファイアウォール セキュリティ機能です。

仮想ネットワークへの Service Bus 名前空間のバインドは、2 ステップのプロセスです。 まず、仮想ネットワーク サブネットに**仮想ネットワーク サービス エンドポイント**を作成し、[サービス エンドポイントの概要][vnet-sep]に関する記事で説明しているように、そのエンドポイントを **Microsoft.ServiceBus** に対して有効にする必要があります。 サービス エンドポイントを追加した後、Service Bus 名前空間を "**仮想ネットワーク規則**" にバインドします。

仮想ネットワーク規則は、Service Bus 名前空間と仮想ネットワーク サブネットの関連付けです。 ルールが存在する間、サブネットにバインドされているすべてのワークロードには、Service Bus 名前空間へのアクセス権が付与されます。 Service Bus 自体は送信接続を確立することはなく、アクセス許可を取得する必要はないので、このルールを有効にすることでサブネットへのアクセス権が付与されることはありません。

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure portal を使用して仮想ネットワーク サービス エンドポイントを追加する方法を示します。 アクセスを制限するには、この Event Hubs 名前空間に対して仮想ネットワーク サービス エンドポイントを統合する必要があります。

1. [Azure portal](https://portal.azure.com) で、ご利用の **Service Bus 名前空間**に移動します。
2. 左側のメニューで、 **[ネットワーク]** オプションを選択します。 既定では、 **[すべてのネットワーク]** オプションが選択されています。 名前空間は、どの IP アドレスからの接続も受け入れます。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。 

    ![ファイアウォールで [すべてのネットワーク] のオプションが選択されている](./media/service-endpoints/firewall-all-networks-selected.png)
1. ページの上部で、 **[選択されたネットワーク]** オプションを選択します。
2. ページの **[仮想ネットワーク]** セクションで、 **[+ 既存の仮想ネットワークを追加]** を選択します。 

    ![既存の仮想ネットワークを追加する](./media/service-endpoints/add-vnet-menu.png)
3. 仮想ネットワークの一覧から仮想ネットワークを選択し、**サブネット**を選択します。 仮想ネットワークを一覧に追加する前に、サービス エンドポイントを有効にする必要があります。 サービス エンドポイントが有効になっていない場合は、有効にするよう求められます。
   
   ![サブネットを選択する](./media/service-endpoints/select-subnet.png)

4. **Microsoft.ServiceBus** に対して、サブネットのサービス エンドポイントが有効になると、次の成功メッセージが表示されます。 ページの下部にある **[追加]** を選択して、ネットワークを追加します。 

    ![サブネットを選択してエンドポイントを有効にする](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > サービス エンドポイントを有効にできない場合は、Resource Manager テンプレートを使用して、不足している仮想ネットワーク サービス エンドポイントを無視してもかまいません。 この機能はポータルでは使用できません。
6. ツール バーの **[保存]** を選択して設定を保存します。 ポータルの通知に確認が表示されるまで、数分間待機します。 **[保存]** ボタンは無効になっているはずです。 

    ![ネットワークを保存する](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Resource Manager テンプレートの使用
次の Resource Manager テンプレートでは、既存の Service Bus 名前空間に仮想ネットワーク規則を追加できます。

テンプレート パラメーター:

* **namespaceName**: Service Bus 名前空間。
* **virtualNetworkingSubnetId**: 仮想ネットワーク サブネットの Resource Manager の完全修飾パス。たとえば、仮想ネットワークの既定のサブネットの場合は `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` です。

> [!NOTE]
> 可能な拒否ルールはありませんが、Azure Resource Manager テンプレートには、接続を制限しない **"Allow"** に設定された既定のアクション セットがあります。
> 仮想ネットワークまたはファイアウォールのルールを作成するときは、***"defaultAction"*** を変更する必要があります。
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> から
> ```json
> "defaultAction": "Deny"
> ```
>

テンプレート:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

## <a name="next-steps"></a>次のステップ

仮想ネットワークについて詳しくは、以下のリンクをご覧ください。

- [Azure 仮想ネットワーク サービス エンドポイント][vnet-sep]
- [Azure Service Bus IP フィルタリング][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
