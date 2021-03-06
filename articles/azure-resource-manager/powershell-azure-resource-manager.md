---
title: "PowerShell で Azure ソリューションを管理する | Microsoft Docs"
description: "Azure PowerShell と Resource Manager を使用してリソースを管理します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: ae5ccb83a0088cb7c9668f18620b74f9f3f1e9b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Azure PowerShell と Resource Manager でリソースを管理する

この記事では、Azure PowerShell と Azure Resource Manager でソリューションを管理する方法について説明します。 Resource Manager に慣れていない場合は、[Resource Manager の概要](resource-group-overview.md)に関するページをご覧ください。 この記事では管理タスクに重点を置いて説明します。 このチュートリアルの内容は次のとおりです。

1. リソース グループの作成
2. リソース グループへのリソースの追加
3. リソースへのタグの追加
4. 名前とタグの値に基づいたリソースへのクエリ実行
5. リソースへのロック適用と解除
6. リソース グループの削除

この記事では、Resource Manager テンプレートをサブスクリプションにデプロイする方法については説明しません。 この情報については、「[Deploy resources with Resource Manager templates and Azure PowerShell (Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ)](resource-group-template-deploy.md)」を参照してください。

## <a name="get-started-with-azure-powershell"></a>Azure PowerShell の使用に関するページ

Azure PowerShell がインストールされていない場合は、[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)に関するページをご覧ください。

前に Azure PowerShell をインストールしていても、最近更新していない場合は、最新バージョンをインストールすることを検討してください。 バージョンの更新は、インストールと同じ方法で行うことができます。 たとえば、Web Platform Installer を使用した場合は、それを再度起動して、更新プログラムを探します。

Azure Resource モジュールのバージョンを確認するには、次のコマンドレットを使用してください。

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

この記事は、バージョン 3.3.0 向けに更新されています。 以前のバージョンを使っている場合は、この記事に示されている手順と一致しない可能性があります。 このバージョンのコマンドレットに関するドキュメントについては、「[AzureRM.Resources Module (AzureRM.Resources モジュール)](/powershell/module/azurerm.resources)」を参照してください。

## <a name="log-in-to-your-azure-account"></a>Azure アカウントへのログイン
ソリューションを操作する前に、ご使用のアカウントにログインする必要があります。

Azure アカウントにログインするには、**Login-AzureRmAccount** コマンドレットを使います。

```powershell
Login-AzureRmAccount
```

このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。

コマンドレットは、自分のアカウントと、タスクに使用するサブスクリプションに関する情報を返します。

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

複数のサブスクリプションがある場合は、別のサブスクリプションに切り替えることができます。 最初に、自分のアカウントのすべてのサブスクリプションを見てみましょう。

```powershell
Get-AzureRmSubscription
```

有効および無効なサブスクリプションを返します。

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

別のサブスクリプションに切り替えるには、**Set-AzureRmContext** コマンドレットでサブスクリプション名を指定します。

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>リソース グループの作成

サブスクリプションにリソースをデプロイする前に、そのリソースを含めるリソース グループを作成する必要があります。

リソース グループを作成するには、 **New-AzureRmResourceGroup** コマンドレットを使用します。 コマンドは **Name** パラメーターを使用してリソース グループの名前を指定し、**Location** パラメーターを使用して場所を指定します。

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

出力の形式は次のとおりです。

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

後でリソース グループを取得する必要がある場合は、次のコマンドレットを使用します。

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

自分のサブスクリプションのリソース グループをすべて取得するには、名前を指定しないでください。

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>リソース グループへのリソースの追加

リソースをリソース グループに追加するには、**New-AzureRmResource** コマンドレット、または作成するリソースの種類に固有のコマンドレット (**New-AzureRmStorageAccount** など) を使用します。 リソースの種類に固有のコマンドレットには、新しいリソースが必要とするプロパティのパラメーターが含まれているため、ほとんどの場合、こちらを使用する方が簡単です。 **New-AzureRmResource** を使用するには、設定するすべてのプロパティを把握しておく必要があります。こうしたプロパティを入力するよう求めるメッセージは表示されません。

ただし、コマンドレットを使ってリソースを追加した場合、その新しいリソースは Resource Manager テンプレートに存在しないため、将来的に混乱が生じる可能性があります。 Microsoft では、Azure ソリューションのインフラストラクチャは、Resource Manager テンプレートを使って定義することをお勧めします。 テンプレートを使用すると、ソリューションを繰り返し、かつ確実にデプロイすることができます。 この記事では、PowerShell コマンドレットでストレージ アカウントを作成しますが、後でリソース グループからテンプレートを生成します。

次のコマンドレットは、ストレージ アカウントを作成します。 この例で示されている名前ではなく、ストレージ アカウントの一意の名前を指定してください。 名前の長さは 3 ～ 24 文字で、使用できるのは数字と小文字のみです。 この例で示されている名前は既に使用済みのため、その名前を使うと、エラーが発生します。

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

後でこのリソースを取得する必要がある場合は、次のコマンドレットを使用します。

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>タグを追加します

タグを使用すると、さまざまなプロパティに基づいてリソースを整理できます。 たとえば、同じ部門に属しているさまざまなリソース グループに、複数のリソースが含まれていることがあります。 こうしたリソースに部門タグと値を適用して、同じカテゴリに属するものとしてマークできます。 また、運用環境で使用されているリソースか、テスト環境のリソースかをマークすることもできます。 この記事では、1 つのリソースにのみタグを適用しますが、環境内では、ほとんどの場合、すべてのリソースにタグを適用すると便利です。

次のコマンドレットでは、2 つのタグをストレージ アカウントに適用します。

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

タグは 1 つのオブジェクトとして更新されます。 タグが既に含まれているリソースにタグを追加するには、最初に既存のタグを取得します。 新しいタグを、既存のタグが含まれるオブジェクトに追加したら、すべてのタグをリソースに再度適用します。

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>リソースの検索

**Find-AzureRmResource** コマンドレットを使用して、さまざまな検索条件でリソースを取得します。

* 名前によってリソースを取得するには、**ResourceNameContains** パラメーターを指定します。

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* リソース グループですべてのリソースを取得するには、**ResourceGroupNameContains** パラメーターを指定します。

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* タグ名と値ですべてのリソースを取得するには、**TagName** パラメーターと **TagValue** パラメーターを指定します。

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* 特定のリソースの種類のリソースをすべて取得するには、**ResourceType** パラメーターを指定します。

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="get-resource-id"></a>リソース ID を取得する

多くのコマンドは、リソース ID をパラメーターとして受け取ります。 リソースおよび変数内のストアの ID を取得するには、次のコマンドを使用します。

```powershell
$webappID = (Get-AzureRmResource -ResourceGroupName exampleGroup -ResourceName exampleSite).ResourceId
```

## <a name="lock-a-resource"></a>リソースのロック

重要なリソースが誤って削除または変更されないようにするには、そのリソースにロックを適用します。 **CanNotDelete** または **ReadOnly** のいずれかを指定できます。

管理ロックを作成または削除するには、`Microsoft.Authorization/*` または `Microsoft.Authorization/locks/*` アクションにアクセスできる必要があります。 組み込みロールのうち、所有者とユーザー アクセス管理者にのみこれらのアクションが許可されています。

ロックを適用するには、次のコマンドレットを使用します。

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

前の例でロックされたリソースは、ロックが解除されるまで削除できません。 ロックを解除するには、次を使用します。

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

ロックの詳細については、[Azure Resource Manager でのリソースのロック](resource-group-lock-resources.md)に関するページをご覧ください。

## <a name="remove-resources-or-resource-group"></a>リソースまたはリソース グループの削除
リソースまたはリソース グループを削除できます。 リソース グループを削除すると、そのリソース グループ内のリソースもすべて削除されます。

* リソース グループからリソースを削除するには、 **Remove-AzureRmResource** コマンドレットを使用します。 このコマンドレットはリソースを削除しますが、リソース グループは削除しません。

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* リソース グループとそのグループのリソースすべてを削除するには、**Remove-AzureRmResourceGroup** コマンドレットを使用します。

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

どちらのコマンドレットについても、リソースまたはリソース グループを削除するかどうかを確認するメッセージが表示されます。 リソースまたはリソース グループが適切に削除されると、**True** が返されます。

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Azure Automation での Resource Manager スクリプトの実行

この記事では、Azure PowerShell でリソースに対して基本的な操作を行う方法について説明します。 高度な管理シナリオでは、通常、スクリプトを作成し、そのスクリプトを、必要に応じてまたはスケジュールに従って再利用する必要があります。 [Azure Automation](../automation/automation-intro.md) を使用すると、よく使用される Azure ソリューション管理スクリプトを自動化できます。

次のトピックでは、Azure Automation、Resource Manager、および PowerShell を使用して、管理タスクを効果的に実行する方法について説明します。

- Runbook 作成の詳細については、「[初めての PowerShell Runbook](../automation/automation-first-runbook-textual-powershell.md)」を参照してください。
- スクリプト ギャラリーの使用については、「[Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](../automation/automation-runbook-gallery.md)」を参照してください。
- 仮想マシンを起動および停止する Rubbook については、「[Azure Automation シナリオ: JSON 形式のタグを使用して Azure VM の起動とシャットダウンのスケジュールを作成する](../automation/automation-scenario-start-stop-vm-wjson-tags.md)」を参照してください。
- ピーク時間外に仮想マシンを起動および停止する Runbook については、「[ピーク時間外 VM 起動/停止 ソリューション (Automation)](../automation/automation-solution-vm-management.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* リソース マネージャーのテンプレートの作成の詳細については、[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* テンプレートをデプロイする方法の詳細については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。
* 新しいリソース グループに、既存のリソースを移動できます。 例については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。

