---
title: 従量制課金に対する異常検出サービス - Microsoft Azure Marketplace
description: 異常検出のしくみ、通知が送信されるタイミングとその対処方法、およびサポート オプションについて説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: anbene
ms.author: mingshen
ms.date: 06/10/2020
ms.openlocfilehash: becd15ceea41e40b35848f46f9657c501acf659a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122032"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>従量制課金に対する異常検出サービス

[マーケットプレース測定サービス](marketplace-metering-service-apis-faq.md)を使用すると、標準以外の単位に応じて課金されるコマーシャル マーケットプレース プログラムでプランを作成できます。 従量制課金では、お客様が顧客の使用量について使用状況イベントを Microsoft に送信し、Microsoft がその使用量に基づいて請求を準備します。

バグ、使用量追跡の構成ミス、不正行為など、さまざまな原因によって間違った使用量データが発生する可能性があります。 間違った使用量データは、不適当な顧客請求および請求紛争につながる可能性があります。

このリスクを軽減するために、Mirosoft の異常検出サービスでは、機械学習アルゴリズムを適用して、通常の従量制課金動作を識別し、従量制課金使用量を分析し、最小限のユーザー操作で異常を検出します。

従量制課金使用量において異常が検出された場合は通知されます。 これにより、異常が実際の問題であることが確認された場合に、それを調査して Microsoft に通知することができます。この時点で、顧客の課金に関する問題に事前に対処するためのアクションを実行できます。

従量制課金使用量の急増、急落、傾向の変化に加えて、Microsoft のモデルでは季節的な影響も考慮されます。 従量制課金は超過データを介して伝達されるため、Microsoft のモデルでは、長期間にわたって不足しているデータを適切に処理することもできます。

異常検出の結果の例を次に示します。 想定される範囲は黄色の帯として表示されます。 許容される従量制課金使用量は、帯内の緑色の星印として表示されます。 帯の外部の課金使用量は赤い点として表示されます。  

予測可能な傾向の外部で検出された異常:

![予測可能な傾向の外部で検出された異常を示します。](media/anomaly-1.png)

繰り返し発生する周期的傾向の外部で検出された異常:

![繰り返し発生する周期的傾向の外部で検出された異常を示します。](media/anomaly-2.png)

上昇傾向で検出された異常:

![上昇傾向で検出された異常を示します。](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>異常検出サービスのしくみ

異常検出は、すべての従量制課金量に対して自動的に有効になります。 使用状況イベントを Microsoft に送信すると、異常検出サービスによって、過去の使用量データに基づいて予測値のモデルが作成されます。 このモデルは毎週実行されます。

異常検出は、測定ごとおよび顧客ごとのレベルで機能します。 つまり、各顧客の各測定に、この顧客のこの測定の過去の使用量パターンに基づいてトレーニングされたモデルがあることを意味します。

モデルは、遡及的な信頼区間を生成することによって機能します。 時系列予測は、傾向予測部分と季節性部分で構成される一般化された加法モデルです。 モデルは回帰タスクとして定式化されているため、長期間にわたって不足しているデータを適切に処理することができます。 観測値が予測された信頼区間の外にある場合、それは、従量制課金の履歴パターンに基づいて観測値を説明することができないため、異常である可能性があることを意味します。

## <a name="anomaly-detection-notification"></a>異常検出通知

異常検出の通知は毎週電子メールで送信されます。 これには、その週にすべての測定と顧客について検出されたすべての異常が含まれます。 この電子メールは、プランの作成時に提供された**エンジニアリング**および**サポート**の連絡先に送信されます。

検出された異常が実際の問題であるかどうかを調査し、そうである場合は Microsoft に連絡して使用量が間違っていることを報告してください (以下のサポートに関するセクションを参照してください)。

検出された異常が通常の使用量であることを確認できた場合は、それ以上のアクションは必要ありません。 ただし、異常が潜在的に高い財務リスクを表す場合、Microsoft は使用量を確認するためにお客様に連絡する場合があります。  

## <a name="when-and-how-to-get-support"></a>サポートを受けるタイミングと方法

お客様が Microsoft に送信した使用量が間違っており、その結果、顧客に対して過少請求が行われたか、そのような結果になる場合でも、Microsoft が過少報告された使用量の請求を顧客に開始したり、その分の使用料をお客様に支払うことはありません。 過少報告による収益の損失は、お客様が引き受ける必要があります。

次のいずれかのケースが適用される場合は、サポート チケットを開いて、顧客に対する返金または請求調整を要求できます。

- 検出された異常の 1 つは実際の問題であり、間違った使用量によって顧客に**過剰請求**されることを確認した。
- 顧客に**過剰請求**される結果になる間違った使用量を送信したことを発見した。
- 顧客の従量制課金使用量の請求に対して返金を要求することを望んでいる。

チケットを送信するには:

1. サポート ページに移動します。 **[Tell us about your issue]\(問題をお知らせください\) ボックス**に、「使用量が間違っている」と入力します。
2. サポート トピックの検索結果のドロップダウンで、次のいずれかを選択します。
    - **[コマーシャル マーケットプレース]**  >  **[Metered Billing]\(従量制課金\)**  >  **[Wrong usage sent for Azure Applications offer]\(送信された Azure アプリケーション プランの使用量が間違っている\)** 、または
    - **[コマーシャル マーケットプレース]**  >  **[Metered Billing]\(従量制課金\)**  >  **[Wrong usage sent for SaaS offer]\(送信された SaaS プランの使用量が間違っている\)**
3. **[次のステップ]** で、 **[Review solutions]\(ソリューションの確認\)** ボタンを選択します。すると、パートナー センターにサインインし、サポート チケットを送信するよう促されます。

発行元サポート オプションについては、「[パートナー センターでのコマーシャル マーケットプレース プログラムのサポート](support.md)」をご覧ください。

## <a name="next-step"></a>次のステップ

- [Marketplace の測定サービス API](marketplace-metering-service-apis.md) について学習する。
