---
title: "クイックスタート: Azure SQL Data Warehouse のコンピューティングの一時停止と再開 - PowerShell | Microsoft Docs"
description: "Azure SQL Data Warehouse のコンピューティングを一時停止してコストを節約する PowerShell タスク。 データ ウェアハウスを使用する準備ができたら、コンピューティングを再開します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/25/2018
ms.author: barbkess
ms.openlocfilehash: e2401f31ad88c8ee5fdd8912ff6033f0619a06b0
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-powershell"></a>クイックスタート: PowerShell での Azure SQL Data Warehouse のコンピューティングの一時停止と再開
PowerShell を使用して、Azure SQL Data Warehouse のコンピューティングを一時停止してコストを節約します。 データ ウェアハウスを使用する準備ができたら、コンピューティングを再開します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

このチュートリアルには、Azure PowerShell モジュール バージョン 5.1.1 以降が必要です。 現在所有しているバージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps.md)に関するページを参照してください。 

## <a name="before-you-begin"></a>開始する前に

このクイック スタートでは、一時停止して再開できる SQL Data Warehouse が既に用意されていることを前提とします。 作成する必要がある場合は、[Azure Portal での作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成できます。 

## <a name="log-in-to-azure"></a>Azure にログインする

[Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Add-AzureRmAccount
```

使用しているサブスクリプションを確認するには、[Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) を実行します。

```powershell
Get-AzureRmSubscription
```

既定ではない別のサブスクリプションを使用する必要がある場合は、[Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription) を実行します。

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>データ ウェアハウスの情報を調べる

一時停止して再開する予定のデータ ウェアハウスのデータベース名、サーバー名、およびリソース グループを見つけます。 

次の手順に従って、データ ウェアハウスの場所の情報を検索します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の左側のページで **[SQL データベース]** を選択します。
3. **[SQL データベース]** ページから **[mySampleDataWarehouse]** を選択します。 データ ウェアハウスが開きます。

    ![サーバー名とリソース グループ](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. データ ウェアハウスの名前 (データベース名) を書き留めます。 サーバー名とリソース グループも書き留めます。 あなたが 
5.  これらは、一時停止コマンドと再開コマンドで使用します。
6. サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として最初の部分のみを使用します。 前の図では、サーバーの完全名は newserver-20171113.database.windows.net です。 サフィックスを削除し、PowerShell コマンドレットのサーバー名として **newserver-20171113** を使用します。

## <a name="pause-compute"></a>コンピューティングの一時停止
コストを節約するために、オンデマンドでコンピューティング リソースを一時停止および再開できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。 データベースが一時停止されている間、コンピューティング リソースへの課金は行われません。 ただし、ストレージに対する課金は引き続き行われます。 

データベースを一時停止するには、[Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase.md) コマンドレットを使用します。 次の例は、**newserver-20171113** という名前のサーバーでホストされている **mySampleDataWarehouse** という名前のデータ ウェアハウスを一時停止します。 このサーバーは、**myResourceGroup** という名前の Azure リソース グループ内にあります。


```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

バリエーションの 1 つとして、次の例では $database オブジェクトにデータベースを取り込みます。 オブジェクトは [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase) にパイプ処理されます。 結果は、オブジェクト resultDatabase に格納されます。 最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>コンピューティングの再開
データベースを開始するには、[Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase) コマンドレットを使用します。 次の例は、newserver-20171113 という名前のサーバーでホストされている mySampleDataWarehouse という名前のデータベースを開始します。 このサーバーは、myResourceGroup という名前の Azure リソース グループ内にあります。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

バリエーションの 1 つとして、次の例では $database オブジェクトにデータベースを取り込みます。 オブジェクトは [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase.md) にパイプ処理され、結果が $resultDatabase に格納されます。 最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

データ ウェアハウス ユニットとデータ ウェアハウスに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。 

- データをストレージ内に保持する場合は、コンピューティングを一時停止します。
- それ以上課金されないようにする場合は、データ ウェアハウスを削除できます。 

必要に応じて、以下の手順でリソースをクリーンアップします。

1. [Azure Portal](https://portal.azure.com) にサインインし、データ ウェアハウスをクリックします。

    ![リソースのクリーンアップ](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. コンピューティング リソースを一時停止するには、**[一時停止]** ボタンをクリックします。 データ ウェアハウスが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、**[開始]** をクリックします。

2. コンピューティング リソースやストレージに課金されないようにデータ ウェアハウスを削除するには、**[削除]** をクリックします。

3. 作成した SQL Server を削除するには、**mynewserver-20171113.database.windows.net** をクリックした後、**[削除]** をクリックします。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、削除には注意してください。

4. リソース グループを削除するには、**myResourceGroup** をクリックして、**[リソース グループの削除]** をクリックします。


## <a name="next-steps"></a>次の手順
データ ウェアハウスに対するコンピューティングの一時停止と再開を行いました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
>[SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)