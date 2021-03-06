---
title: Windows Virtual Desktop 用に Azure Multi-Factor Authentication を設定する - Azure
description: Windows Virtual Desktop でセキュリティを強化するために Azure Multi-Factor Authentication を設定する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16abe8d155a0d7d7f65c69e6305da62bd8813ea4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361151"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Windows Virtual Desktop 用に Azure Multi-Factor Authentication を有効にする

Windows Virtual Desktop 用の Windows クライアントは、Windows Virtual Desktop をローカル コンピューターに統合するための優れたオプションです。 ただし、Windows Virtual Desktop アカウントを Windows クライアントに構成する場合、自分自身とユーザーの安全を維持するために必要な特定の手段があります。

初めてサインインすると、クライアントによって、ユーザー名、パスワード、Azure MFA が要求されます。 その後、次回のサインイン時に、クライアントで、Azure Active Directory (AD) エンタープライズ アプリケーションからのトークンが記憶されます。 **[このアカウントを記憶する]** を選択した場合、ユーザーは、クライアントの再起動後に、資格情報を再入力しなくてもサインインでき ます。

資格情報を記憶させることは便利ですが、企業のシナリオや個人のデバイスでのデプロイのセキュリティを低下させる可能性もあります。 ユーザーを保護するには、クライアントが Azure Multi-Factor Authentication (MFA) 資格情報を要求し続けることを確認する必要があります。 この記事では、Windows Virtual Desktop の条件付きアクセス ポリシーを構成して、この設定を有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

使用を開始するために必要なものを次に示します。

- Azure Active Directory Premium P1 または P2 を含むライセンスをユーザーに割り当てます。
- ユーザーがグループ メンバーとして割り当てられている Azure Active Directory グループ。
- すべてのユーザーに対して Azure MFA を有効にします。 その方法の詳細については、「[ユーザーに 2 段階認証を要求する方法](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)」を参照してください。

> [!NOTE]
> 次の設定は、[Windows Virtual Desktop Web クライアント](https://rdweb.wvd.microsoft.com/webclient/index.html)にも適用されます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

このセクションでは、Windows Virtual Desktop に接続するときに多要素認証を必要とする条件付きアクセス ポリシーを作成する方法について説明します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
3. **[新しいポリシー]** を選択します。
4. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
5. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   - **[Include]\(含める\)** で **[ユーザーとグループの選択]**  >  **[ユーザーとグループ]** を選択し、事前準備の段階で作成したグループを選択します。
   - **[Done]** を選択します。
6. **[クラウド アプリまたはアクション]**  >  **[Include]\(含める\)** で、 **[アプリを選択]** を選択します。
   - **[Windows Virtual Desktop]** (App ID 9cdead84-a844-4324-93f2-b2e6bb768d07) を選択し、 **[選択]** 、 **[完了]** の順に選択します。

     > [!div class="mx-imgBorder"]
     > ![[クラウド アプリまたはアクション] ページのスクリーンショット。 Windows Virtual Desktop および Windows Virtual Desktop Client アプリが赤色で強調表示されています。](media/cloud-apps-enterprise.png)

     >[!NOTE]
     >選択するアプリのアプリ ID を検索するには、 **[エンタープライズ アプリケーション]** に移動し、[アプリケーションの種類] ドロップダウン メニューから **[Microsoft アプリケーション]** を選択します。

7. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
8. **[アクセス制御]**  >  **[セッション]** で、 **[サインインの頻度]** を選択し、値を **[1]** に、単位を **[時間]** に設定してから **[Select]\(選択する\)** を選択します。
9. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
10. **[作成]** を選択して、ポリシーを有効化します。

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス ポリシーの詳細について確認する](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [ユーザー サインインの頻度の詳細について確認する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
