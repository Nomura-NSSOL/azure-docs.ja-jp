---
title: アプリケーションにユーザーを割り当てる方法 | Microsoft Docs
description: ユーザーがテナントでアプリケーションに割り当てられる方法について理解します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45124862fffa3a1ef6f601733407fbbea4eb5e74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84762974"
---
# <a name="how-to-assign-users-to-applications"></a>ユーザーをアプリケーションに割り当てる方法

この記事では、ユーザーがテナントでアプリケーションに割り当てられる方法について説明します。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>ユーザーが Azure AD でアプリケーションに割り当てられる方法

ユーザーがアプリケーションにアクセスするには、まず何らかの方法でそのユーザーを割り当てる必要があります。 割り当ては管理者またはビジネス デリゲートによって行われますが、場合によってはユーザー自身で実行できることもあります。 次に、ユーザーをアプリケーションに割り当てることができる方法について説明します。

1.  管理者が、アプリケーションに直接[ユーザーを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。

2.  管理者が、アプリケーションにユーザーがメンバーとなっている[グループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。次のグループが含まれます。

    * オンプレミスから同期されたグループ

    * クラウドで作成された静的なセキュリティ グループ

    * クラウドで作成された[動的なセキュリティ グループ](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

    * クラウドで作成された Office 365 グループ

    * [すべてのユーザー](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) グループ

3.  管理者が [[アプリケーションのセルフ サービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) を有効にして、**ビジネス承認なし**でユーザーが[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)の **[アプリの追加]** 機能を使用してアプリケーションを追加することを許可します。

4.  管理者が [[アプリケーションのセルフ サービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) を有効にして、ユーザーが[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)の **[アプリの追加]** 機能を使用してアプリケーションを追加することを許可しますが、**選択された一連のビジネス承認者からの事前の承認があった**場合に限ります。

5.  管理者が [[セルフサービスによるグループ管理]](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) を有効にして、アプリケーションが**ビジネス承認なし**で割り当てられているグループにユーザーが参加することを許可します。

6.  管理者が [[セルフサービスによるグループ管理]](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) を有効にして、アプリケーションが割り当てられているグループにユーザーが参加することを許可しますが、**選択された一連のビジネス承認者からの事前の承認があった**場合に限ります。

7.  管理者は、[Microsoft Office 365](https://products.office.com/) などのファースト パーティ アプリケーションのライセンスを直接ユーザーに割り当てます。

8.  管理者は、[Microsoft Office 365](https://products.office.com/) などのファースト パーティ アプリケーションのライセンスを、ユーザーがメンバーとなっているグループに割り当てます。

9.  [管理者がすべてのユーザーによるアプリケーションの使用に同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)したら、ユーザーはアプリケーションにサインインします。

10. ユーザーはアプリケーションにサインインすることで、[アプリケーションに同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)します。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
