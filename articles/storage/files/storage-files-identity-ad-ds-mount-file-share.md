---
title: AD DS に参加している VM に Azure ファイル共有をマウントする
description: オンプレミスの Active Directory Domain Services に参加しているマシンにファイル共有をマウントする方法について学習します。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 9a8805666e1e162f76cf5fa6f7d828833c573bed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510449"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>パート 4: ドメインに参加している VM からファイル共有をマウントする

この記事を始める前に、前の [SMB 経由でディレクトリおよびファイル レベルのアクセス許可を構成する方法](storage-files-identity-ad-ds-configure-permissions.md)に関する記事を完了していることを確認してください。

この記事で説明するプロセスでは、ファイル共有とアクセス許可が正しく設定されていることと、ドメインに参加している VM から Azure ファイル共有にアクセスできることを確認します。 共有レベルの RBAC ロールの割り当ては、有効になるまでに時間がかかる場合があります。 

次の図のように、アクセス許可を付与した資格情報を使用してクライアントにサインインします。

![ユーザー認証のための Azure AD サインイン画面を示すスクリーン ショット](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>前提条件のマウント

ファイル共有をマウントする前に、次の前提条件を完了していることを確認してください。

- 対象のストレージ アカウント キーを使用してファイル共有を以前にマウントしたクライアントからファイル共有をマウントする場合は、その共有を切断したこと、ストレージ アカウント キーの永続的な資格情報を削除したこと、および認証に AD DS 資格情報を現在使用していることを確認してください。
- クライアントには、お使いの AD DS への通信経路が必要です。 お使いのマシンまたは VM が AD DS によって管理されているネットワークの外にある場合、VPN を有効にして、認証のために AD DS に到達できるようにする必要があります。

プレースホルダーの値を実際の値に置き換えて次のコマンドを使用して Azure ファイル共有をマウントします。

```cli
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

AD DS の資格情報を使用したマウントで問題が発生した場合は、「[AD 資格情報を使用して Azure Files をマウントできない](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)」を参照してください。

対象のファイル共有のマウントに成功した場合は、対象の Azure ファイル共有に対してオンプレミスの AD DS 認証が正常に有効化され、構成されています。

## <a name="next-steps"></a>次のステップ

ストレージ アカウントを表すために AD DS で作成した ID が、パスワードのローテーションが適用されるドメインまたは OU 内にある場合は、次の記事に進み、パスワードを更新する手順を確認してください。

[AD DS のストレージ アカウント ID のパスワードを更新します](storage-files-identity-ad-ds-update-password.md)