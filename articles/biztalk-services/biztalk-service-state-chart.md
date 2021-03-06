---
title: "BizTalk Services のさまざまな状態またはステータスで許可されるタスク | Microsoft Docs"
description: "MABS のさまざまな状態で許可されるアクションと操作: 停止、開始、再起動、中断、再開、削除、スケール、構成の更新、バックアップ"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 05470e75fc7b46603c8fce3a98c66ac6a24758a8
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>BizTalk サービスの状態を使用して実行できる操作と実行できない操作

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

BizTalk サービスの現在の状態によっては、BizTalk サービスで実行できる操作と実行できない操作があります。

たとえば、新しい BizTalk サービスをプロビジョニングするとします。 正常に完了すると、BizTalk サービスが `active` 状態になります。 アクティブ状態では、BizTalk サービスを停止、中断、および削除できます。 BizTalk サービスを停止し、停止に失敗すると、BizTalk サービスは `StopFailed` 状態になります。 `StopFailed` 状態では、BizTalk サービスを再起動できます。 許可されていない操作 (再開など) を実行しようとすると、次のエラーが発生します。

`Operation not allowed`

## <a name="view-the-possible-states"></a>状態の表示

次の表に、BizTalk サービスの各状態で実行できる操作またはアクションを示します。 ✔ は、その状態で許可される操作を意味します。 空のエントリは、その状態で実行できない操作を意味します。

| サービスの状態 | 開始 | 停止 | 再起動 | 中断 | 再開 | 削除 | スケール | 更新 <br/> 構成 | バックアップ |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| アクティブ |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| 無効 |  |  |  |  |  | ✔ | |  |  | 
| Suspended |  |  |  |  | ✔ | ✔ | |  | ✔ |
| 停止済み | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Service Update Failed |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>関連項目
* [BizTalk Services の [ダッシュボード]、[監視]、[スケール] の各タブの操作](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services の Developer、Basic、Standard、および Premium Edition のチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk サービスをバックアップおよび復元する方法](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services でのスロットル](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [BizTalk サービスに対して Service Bus および Access Control の発行者名と発行者キーの値を取得](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)

