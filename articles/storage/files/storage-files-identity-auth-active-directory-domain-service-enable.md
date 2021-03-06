---
title: Azure AD Domain Services を使用して SMB 経由でファイル データへのアクセスを承認する
description: Azure Active Directory Domain Services を使用して Azure Files に対する Server Message Block (SMB) 経由の ID ベースの認証を有効にする方法について説明します。 ドメインに参加している Windows 仮想マシン (VM) は、Azure AD の資格情報を使用して Azure ファイル共有にアクセスできます。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fe2bbc1d6f42819354f48812a34371a49e5acbac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999634"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Files に対する Azure Active Directory Domain Services 認証を有効にする

[Azure Files](storage-files-introduction.md)  では、オンプレミス Active Directory Domain Services (AD DS) と Azure Active Directory Domain Services (Azure AD DS) という 2 種類のドメイン サービスを介した、サーバー メッセージ ブロック (SMB) 経由の ID ベースの認証がサポートされます。[「しくみ」セクション](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works)を参照して、認証用の適切なドメイン サービスを選択することを強くお勧めします。 選択するドメイン サービスによって設定は異なります。 この記事では、Azure ファイル共有での認証用に Azure AD DS を有効にして構成する方法に重点を置いて説明します。

Azure ファイル共有を初めて使用する場合は、次の一連の記事を読む前に、[計画ガイド](storage-files-planning.md)に目を通すことをお勧めします。

> [!NOTE]
> Azure Files では、Azure AD DS と RC4-HMAC 暗号化を使用した Kerberos 認証がサポートされています。 AES Kerberos 暗号化はまだサポートされていません。
> Azure Files では、Azure AD と完全に同期する Azure AD DS の認証がサポートされています。 Azure AD DS で範囲指定された同期を有効にし、Azure AD から限定された ID のセットのみを同期する場合、認証と承認はサポートされていません。

## <a name="prerequisites"></a>前提条件

Azure ファイル共有への SMB 経由の Azure AD を有効にする前に、次の前提条件を完了していることを確認してください。

1.  **Azure AD テナントを選択または作成します。**

    SMB を使用した Azure AD 認証には、新規または既存のテナントを使用できます。 アクセスするテナントとファイル共有は、同じサブスクリプションに関連付けられている必要があります。

    新しく Azure AD テナントを作成するには、[Azure AD テナントと Azure AD サブスクリプションを追加](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)できます。 既存の Azure AD テナントがあるが、Azure ファイル共有で使用するために新しいテナントを作成する場合は、[Azure Active Directory テナントの作成](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)に関する記事を参照してください。

1.  **Azure AD テナントで Azure AD Domain Services を有効にします。**

    Azure AD 資格情報を使用した認証をサポートするには、Azure AD テナントの Azure AD Domain Services を有効にする必要があります。 Azure AD テナントの管理者でない場合は、管理者に連絡し、[Azure portal を使用した Azure Active Directory Domain Services の有効化](../../active-directory-domain-services/tutorial-create-instance.md)に関する記事に書かれている手順を実行します。

    通常、Azure AD DS のデプロイが完了するまでには 15 分ほどかかります。 次の手順に進む前に、Azure AD DS の正常性状態が**実行中**と表示されており、パスワード ハッシュ同期が有効になっていることを確認します。

1.  **Azure AD DS を使用している Azure VM ドメインに参加します。**

    VM から Azure AD の資格情報を使用してファイル共有にアクセスするには、VM が Azure AD DS のドメインに参加している必要があります。 VM のドメイン参加方法の詳細については、「[Windows Server 仮想マシンのマネージド ドメインへの参加](../../active-directory-domain-services/join-windows-vm.md)」を参照してください。

    > [!NOTE]
    > SMB を使用した Azure ファイル共有の Azure AD DS 認証は、Windows 7 または Windows Server 2008 R2 以降の OS バージョンで実行されている Azure VM でのみサポートされます。

1.  **Azure ファイル共有を選択または作成します。**

    Azure AD テナントと同じサブスクリプションに関連付けられている新規または既存のファイル共有を選択します。 新しいファイル共有を作成する方法の詳細については、「[Azure Files にファイル共有を作成する](storage-how-to-create-file-share.md)」を参照してください。
    パフォーマンスを最適化するには、共有にアクセスする予定の VM と同じリージョンにファイル共有を配置することをお勧めします。

1.  **ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることにより、Azure Files の接続を確認します。**

    VM とファイル共有が正しく構成されていることを確認するには、ストレージ アカウント キーを使用してファイル共有をマウントします。 詳細については、「[Windows で Azure ファイル共有をマウントして共有にアクセスする](storage-how-to-use-files-windows.md)」を参照してください。

## <a name="regional-availability"></a>リージョン別の提供状況

Azure AD DS を使用した Azure Files 認証は、[すべての Azure パブリック リージョン](https://azure.microsoft.com/global-infrastructure/locations/)で利用できます。

## <a name="overview-of-the-workflow"></a>ワークフローの概要

SMB を使用した Azure ファイル共有への Azure AD DS 認証を有効にする前に、Azure AD と Azure Storage 環境が正しく構成されていることを確認してください。 [前提条件](#prerequisites)を参照して、必要な手順をすべて完了したことを確認することをお勧めします。

次に、以下の手順を実行して、Azure AD 資格情報を使用して Azure Files のリソースへのアクセス許可を付与します。

1. 関連付けられた Azure AD DS デプロイにストレージ アカウントを登録するため、SMB を使用した、ストレージ アカウントの Azure AD DS 認証を有効にします。
2. Azure AD の ID (ユーザー、グループ、またはサービス プリンシパル) に、共有のアクセス許可を割り当てます。
3. SMB を使用したディレクトリおよびファイルへの NTFS アクセス許可を構成します。
4. ドメインに参加している VM から Azure ファイル共有をマウントします。

次の図は、SMB を使用して Azure Files への Azure AD DS 認証を有効にするためのエンドツーエンドのワークフローを示しています。

![SMB を使用した Azure Files への Azure AD ワークフローを示す図](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1.アカウントへの Azure AD DS 認証を有効にする

Azure Files への SMB 経由の Azure AD DS 認証を有効にするには、Azure portal、Azure PowerShell、または Azure CLI を使用して、ストレージ アカウントでプロパティを設定できます。 このプロパティを設定すると、関連付けられている Azure AD DS のデプロイにより、ストレージ アカウントが暗黙的に "ドメイン参加" します。 その後、ストレージ アカウント内のすべての新規および既存のファイル共有に対する、SMB を使用した Azure AD DS 認証が有効になります。

SMB を使用した Azure AD DS 認証を有効にするには、Azure AD テナントに Azure AD DS が正常にデプロイされている必要があることに注意してください。 詳細については、[前提条件](#prerequisites)を参照してください。

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com) を使用して SMB 経由の Azure AD DS 認証を有効にするには、次の手順に従います。

1. Azure portal で、既存のストレージ アカウントに移動するか、または[ストレージ アカウントを作成](../common/storage-account-create.md)します。
1. **[設定]** セクションで、 **[構成]** を選択します。
1. **[Identity-based access for file shares]\(ファイル共有への ID ベースのアクセス\)** で、 **[Azure Active Directory Domain Service (AAD DS)]** のトグルを **[有効]** に切り替えます。
1. **[保存]** を選択します。

次の図は、ストレージ アカウントへの SMB 経由の Azure AD DS 認証を有効にする方法を示しています。

![Azure portal で SMB 経由の Azure AD DS 認証を有効にする](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Azure PowerShell を使用して SMB 経由で Azure AD DS 認証を有効にするには、最新の Az モジュール (2.4 以降) または Az.Storage モジュール (1.5 以降) をインストールします。 PowerShell のインストールの詳細については、[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-Az-ps)に関する記事を参照してください。

新しいストレージ アカウントを作成するには、[New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0) を呼び出し、**EnableAzureActiveDirectoryDomainServicesForFile** パラメーターを **true** に設定します。 以下の例のプレースホルダーをお客様独自の値に置き換えてください。 (以前のプレビュー モジュールを使用していた場合、機能を有効にするためのパラメーターは **EnableAzureFilesAadIntegrationForSMB** です。)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

既存のストレージ アカウントでこの機能を有効にするには、次のコマンドを使用します。

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して SMB 経由の Azure AD 認証を有効にするには、最新バージョンの CLI (バージョン2.0.70 以降) をインストールします。 Azure CLI のインストール方法については、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

新しいストレージ アカウントを作成するには、[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) を呼び出し、`--enable-files-aadds` プロパティを **true** に設定します。 以下の例のプレースホルダーをお客様独自の値に置き換えてください。 (以前のプレビュー モジュールを使用していた場合、機能を有効にするためのパラメーターは **file-aad** です)。

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

既存のストレージ アカウントでこの機能を有効にするには、次のコマンドを使用します。

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

これで、SMB を使用した Azure AD DS 認証が有効になり、Azure ファイル共有へのアクセスを提供するカスタム ロールが Azure AD の ID に割り当てられました。 ファイル共有へのアクセスを他のユーザーに許可する場合は、ID を使用するための[アクセス許可の割り当て](#2-assign-access-permissions-to-an-identity)に関する指示と、「[SMB 経由の NTFS アクセス許可を構成する](#3-configure-ntfs-permissions-over-smb)」のセクションの手順に従ってください。

## <a name="next-steps"></a>次のステップ

Azure Files や、SMB 経由で Azure AD を使用する方法の詳細については、これらのリソースを参照してください。

- [SMB アクセスに対する Azure Files ID ベース認証サポートの概要](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
