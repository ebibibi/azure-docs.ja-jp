﻿---
title: "フェールオーバー グループとアクティブ geo レプリケーション - Azure SQL Database | Microsoft Docs"
description: "アクティブ geo レプリケーションで自動フェールオーバー グループを使用して、障害発生時に 自動フェイル オーバーを実行できるようにします。"
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/11/2017
ms.author: sashan
ms.openlocfilehash: 7d731865ae8da9e1ae9e9f11eef814b86fc10c64
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>概要: フェールオーバー グループとアクティブ geo レプリケーション
アクティブ geo レプリケーションにより、同じまたは異なるデータ センターの場所 (リージョン) に最大 4 つの読み取り可能なセカンダリ データベースを構成できます。 セカンダリ データベースは、データ センターで障害が発生した場合やプライマリ データベースに接続できない場合のクエリとフェールオーバーに使用できます。 フェールオーバーはユーザーのアプリケーションによって手動で開始する必要があります。 フェールオーバー後、新しいプライマリには別の接続エンドポイントが設定されます。 

> [!NOTE]
> 全リージョンのすべてのサービス レベルのすべてのデータベースでアクティブ geo レプリケーションを使用できるようになりました。
>  

Azure SQL Database の自動フェールオーバー グループ (プレビュー段階) は、大規模な geo レプリケーションのリレーションシップ、接続、およびフェールオーバーを自動的に管理するように設計された、SQL Database の機能です。 これにより、ユーザーはプライマリ リージョンで発生した致命的な障害やその他計画外のイベントにより SQL Database サービスの可用性がすべてまたは一部失われたときに、セカンダリ リージョンの複数の関連データベースを自動的に復元できます。 さらに、読み取り可能なセカンダリ データベースを使用して、読み取り専用ワークロードをオフロードできます。  自動フェールオーバー グループには複数のデータベースが関与するため、プライマリ サーバーに構成する必要があります。 プライマリとセカンダリの両方のサーバーは、同一のサブスクリプションである必要があります。 自動フェールオーバー グループでは、グループ内のすべてのデータベースを別のリージョンの 1 つのセカンダリ サーバーにのみレプリケートできます。 自動フェールオーバー グループのないアクティブ geo レプリケーションは、任意のリージョンの最大 4 つのセカンダリを使用できます。

アクティブ geo レプリケーションを使用しており、何らかの理由でプライマリ データベースにエラーが発生したか、単にプライマリ データベースをオフラインにする必要がある場合、任意のセカンダリ データベースでフェールオーバーを開始できます。 セカンダリ データベースの 1 つに対してフェールオーバーがアクティブな場合、その他すべてのセカンダリ データベースは新しいプライマリ データベースに自動的にリンクします。 データベースの復旧に自動フェールオーバー グループ (プレビュー段階) を使用しており、グループ内で 1 つ以上のデータベースに影響を及ぼす機能停止が発生すると、自動フェールオーバーが発生します。 アプリケーションのニーズに最適な自動フェールオーバー ポリシーを構成するか、オプトアウトして手動でのアクティブ化を使用できます。 さらに、自動フェールオーバー グループ (プレビュー段階) には、フェールオーバー中にそのまま残る読み取り/書き込みリスナー エンドポイントと読み取り専用リスナー エンドポイントが用意されています。 手動または自動フェールオーバーのどちらをアクティブ化するにしても、フェールオーバーはグループ内のすべてのデータベースをプライマリに切り替えます。 データベースのフェールオーバーが完了すると、DNS レコードが自動的に更新され、エンドポイントが新しいリージョンにリダイレクトされます。

サーバー上またはエラスティック プール内の個々のデータベースまたはデータベースのセットのレプリケーションとフェールオーバーは、アクティブ geo レプリケーションを使用して管理できます。 そのためには以下のものを使うことができます。 

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: 単一データベース](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: エラスティック プール](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: フェールオーバー グループ](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: 単一データベースまたはエラスティック プール](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: 単一データベース](/rest/api/sql/replicationlinks/failover)
- [REST API: フェールオーバー グループ](/rest/api/sql/failovergroups/failover) 
 
フェールオーバー後は、サーバーおよびデータベースの認証要件が新しいプライマリで構成されていることを確認してください。 詳細については、 [障害復旧後の SQL Database のセキュリティ](sql-database-geo-replication-security-config.md)に関するページを参照してください。 これは、アクティブ geo レプリケーションと自動フェールオーバー グループ (プレビュー段階) の両方に該当します。

アクティブ geo レプリケーションは SQL サーバーの [AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) テクノロジーを活用し、Read Committed スナップショット分離 (RCSI) を使用してプライマリ データベース上のコミットされたトランザクションを非同期的にレプリケートします。 自動フェールオーバー グループにはアクティブ geo レプリケーションに加えてグループ セマンティックが用意されていますが、同じ非同期レプリケーション メカニズムを使用します。 特定の時点におけるセカンダリ データベースは、プライマリ データベースよりもわずかに古い可能性がありますが、セカンダリ データには部分トランザクションが含まれないことが保証されます。 リージョン間で冗長性が確保されるため、自然災害、致命的なヒューマン エラー、または悪意のある行為によってデータセンター全体またはその一部の機能が完全に失われた場合でも、アプリケーションをすばやく復旧できます。 特定の RPO データについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをご覧ください。

次の図は、プライマリが米国中北部リージョン、セカンダリが米国中南部リージョンに構成されたアクティブ Geo レプリケーションの例を示しています。

![geo レプリケーションのリレーションシップ](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

セカンダリ データベースは読み取り可能であるため、ジョブを報告するなどの読み取り専用ワークロードの負荷を軽減するために使用できます。 アクティブ geo レプリケーションを使用している場合、セカンダリ データベースをプライマリと同じリージョンに作成できますが、致命的な障害に対するアプリケーションの復元力は改善されません。 自動フェールオーバー グループ (プレビュー段階) を使用している場合、セカンダリ データベースは常に異なるリージョンに作成されます。

アクティブ geo レプリケーションは、ディザスター リカバリーに加えて次のシナリオで使用できます。

* **データベースの移行**: アクティブ geo レプリケーションを使用して、最小限のダウンタイムでデータベースをあるサーバーから別のサーバーにオンラインで移行できます。
* **アプリケーションのアップグレード**: アプリケーションのアップグレード時に、セカンダリをフェールバック コピーとして余分に作成することができます。

ビジネスの継続性を実現するにあたって、データセンター間のデータベース冗長性を追加することは、ソリューションのほんの一部です。 致命的な障害の後にアプリケーション (サービス) をエンド ツー エンドで復旧するには、そのサービスと依存しているサービスを構成するすべてのコンポーネントを復旧する必要があります。 このようなコンポーネントの例には、クライアント ソフトウェア (カスタム JavaScript が設定されたブラウザーなど)、Web フロント エンド、ストレージ、DNS などがあります。 すべてのコンポーネントが同じ障害に対する復元性を備えており、アプリケーションの目標復旧時間 (RTO) 以内に利用可能になることが重要です。 そのため、依存するサービスをすべて特定し、これらのサービスが提供する保証と機能について把握しておく必要があります。 そのうえで、依存するサービスのフェールオーバー中もサービスが確実に機能するように対策を講じる必要があります。 ディザスター リカバリーのソリューション設計の詳細については、[アクティブ geo レプリケーションを使用したディザスター リカバリー用のクラウド ソリューションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)に関するページを参照してください。

## <a name="active-geo-replication-capabilities"></a>アクティブ geo レプリケーションの機能
アクティブ geo レプリケーションには、次の重要な機能が用意されています。
* **自動非同期レプリケーション**: セカンダリ データベースは、既存のデータベースに追加することによってのみ作成できます。 セカンダリは任意の Azure SQL Database サーバーに作成できます。 作成した後、プライマリ データベースからコピーしたデータをセカンダリ データベースに設定できます。 このプロセスはシード処理と呼ばれます。 オンライン セカンダリ データベースが作成およびシードされた後、プライマリ データベースの更新がセカンダリ データベースに非同期で自動的にレプリケートされます。 非同期レプリケーションでは、トランザクションはプライマリ データベースでコミットされた後に、セカンダリ データベースにレプリケートされます。 
* **読み取り可能なセカンダリ データベース**: アプリケーションは、プライマリ データベースへのアクセスに使用されているのと同じ、または異なるセキュリティ プリンシパルを使用して、セカンダリ データベースにアクセスして読み取り専用操作を実行できます。 セカンダリ データベースは、セカンダリ上で実行されるクエリによってプライマリ (ログの再生) の更新プログラムのレプリケーションが遅延しないように、スナップショット分離モードで動作します。

   > [!NOTE]
   > プライマリ データベースでスキーマの更新がある場合、セカンダリ データベースのログ再生は遅延します。 後者の場合は、セカンダリ データベースでスキーマ ロックが必要です。 
   > 

* **複数の読み取り可能なセカンダリ**: 2 つ以上のセカンダリ データベースを使用すると、プライマリ データベースとアプリケーションの冗長性が向上し、保護レベルが強化されます。 セカンダリ データベースが複数存在する場合、セカンダリ データベースのいずれかに障害が発生しても、アプリケーションは保護された状態が維持されます。 セカンダリ データベースが 1 つしか存在しない場合に障害が発生すると、新しいセカンダリ データベースが作成されるまで、アプリケーションは高いリスクにさらされます。

   > [!NOTE]
   > グローバルに分散されたアプリケーションの構築にアクティブ geo レプリケーションを使用し、4 つを超えるリージョンにデータへの読み取り専用アクセスを提供する必要がある場合は、セカンダリのセカンダリ (チェーンと呼ばれるプロセス) を作成できます。 この方法により、データベースのレプリケーションを事実上無制限に拡張できます。 さらに、チェーンによりプライマリ データベースからのレプリケーションのオーバーヘッドが軽減されます。 トレードオフは、リーフの多いセカンダリ データベースでレプリケーションのラグが増えることです。 
   >

* **エラスティック プール データベースのサポート**: エラスティック プールでは、任意のデータベースに対して、アクティブ geo レプリケーションを構成することができます。 セカンダリ データベースは、別のエラスティック プールに属していてもかまいません。 通常のデータベースの場合、サービス階層が同じであれば、セカンダリがエラスティック プールになったり、その逆になったりすることができます。 
* **セカンダリ データベースの構成可能なパフォーマンス レベル**: プライマリ データベースとセカンダリ データベースはどちらも、同じサービス レベル (Basic、Standard、Premium) である必要があります。 セカンダリ データベースは、プライマリより低いパフォーマンス レベル (DTU) で作成できます。 このオプションは、データベース書き込みアクティビティが高いアプリケーションにはお勧めできません。レプリケーション遅延が増大する可能性があり、フェールオーバー後に深刻なデータ損失のリスクが高くなるためです。 さらに、フェールオーバー後に、新しいプライマリがより高いパフォーマンス レベルにアップグレードされるまで、アプリケーションのパフォーマンスに影響が生じます。 Azure Portal 上のログ IO の割合グラフを使用すると、レプリケーションの負荷を維持するために必要なセカンダリの最小パフォーマンス レベルを適切に見積もることができます。 たとえば、プライマリ データベースが P6 (1000 DTU) で、ログ IO の割合が 50% の場合、セカンダリは P4 (500 DTU) 以上である必要があります。 ログ IO データは、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) または [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) データベース ビューを使用しても取得できます。  SQL Database のパフォーマンス レベルの詳細については、[SQL Database のオプションとパフォーマンス](sql-database-service-tiers.md)に関するページをご覧ください。 
* **ユーザー制御のフェールオーバーとフェールバック**: アプリケーションまたはユーザーによって、セカンダリ データベースをプライマリ ロールにいつでも明示的に切り替えることができます。 実際のシステム停止時には、"計画されていない" オプションを使用して、セカンダリをプライマリにすぐに昇格させる必要があります。 障害が発生したプライマリが回復して、再び使用可能になると、回復したプライマリは自動的にセカンダリとしてマークされ、新しいプライマリによって最新の状態になります。 レプリケーションは非同期であるため、最新の変更をセカンダリにレプリケートする前に、プライマリに障害が発生した場合、計画されていないフェールオーバー時に少量のデータの失われる可能性があります。 複数のセカンダリを持つプライマリがフェールオーバーすると、システムは自動的にレプリケーション関係を再構成して、いかなるユーザー介入も必要とすることなく、残りのセカンダリを新しく昇格されたプライマリにリンクします。 フェールオーバーの原因となった障害が解決されたら、アプリケーションをプライマリ リージョンに復帰させることが望ましい場合があります。 そうするには、"計画" オプションを使用してフェールオーバー コマンドを呼び出す必要があります。 
* **資格情報とファイアウォール規則の同期を保つ**: geo レプリケートされたデータベースには、[データベースのファイアウォール規則](sql-database-firewall-configure.md)の使用をお勧めします。この規則は、データベースと共にレプリケートされ、すべてのセカンダリ データベースのファイアウォールの規則がプライマリと同じになります。 このアプローチにより、プライマリとセカンダリ データベースの両方をホストするサーバー上で、顧客がファイアウォール規則を手動で構成、管理する必要性がなくなります。 同様に、データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使用することにより、プライマリとセカンダリの両方のデータベースが、確実かつ常に同じユーザー資格情報を持つようにして、フェールオーバー時に、ログインとパスワードの不一致による中断を防ぐことができます。 [Azure Active Directory](../active-directory/active-directory-whatis.md) の顧客を追加すると、プライマリおよびセカンダリ データベースへのユーザー アクセスを管理でき、データベース内で資格情報を管理する必要が完全になくなります。

## <a name="auto-failover-group-capabilities"></a>自動フェールオーバー グループの機能

自動フェールオーバー グループの機能は、グループ レベルのレプリケーションと自動フェールオーバーをサポートすることで、アクティブ geo レプリケーションの強力な抽象化を提供します。 さらに、追加のリスナー エンドポイントを提供することにより、フェールオーバー後に SQL 接続文字列を変更する手間を省きます。 

* **フェールオーバー グループ**: 1 つまたは複数のフェールオーバー グループを異なるリージョンの 2 つのサーバー (プライマリおよびセカンダリ サーバー) 間に作成できます。 各グループには、プライマリ リージョンに発生した機能停止によりすべてまたは一部のプライマリ データベースが使用できなくなった際にユニットとして復元できる、1 つまたは複数のデータベースを含めることができます。  
* **プライマリ サーバー**: フェールオーバー グループのプライマリ データベースのホストとなるサーバー。
* **セカンダリ サーバー**: フェールオーバー グループのセカンダリ データベースのホストとなるサーバー。 セカンダリ サーバーをプライマリ サーバーと同じリージョンに配置することはできません。
* **フェールオーバー グループへのデータベースの追加**: 1 つのサーバー内または 1 つのエラスティック プール内の複数のデータベースを同じフェールオーバー グループに置くことができます。 グループにスタンドアロン データベースを追加すると、同じエディションとパフォーマンス レベルを使用してセカンダリ データベースが自動的に作成されます。 プライマリ データベースがエラスティック プール内にある場合、セカンダリは同じ名前のエラスティック プールに自動的に作成されます。 セカンダリ サーバーにセカンダリ データベースが既に存在するデータベースを追加する場合、その geo レプリケーションがグループに継承されます。

   > [!NOTE]
   > フェールオーバー グループの一部でないサーバーにセカンダリ データベースが既に存在するデータベースを追加すると、セカンダリ サーバーに新しいセカンダリが作成されます。 
   >

* **フェールオーバー グループの読み取り/書き込みリスナー**: 現プライマリ サーバーの URL を示す **&lt;failover-group-name&gt;.database.windows.net** という形式の DNS の CNAME レコードです。 これにより、フェールオーバー後にプライマリが変更されたときに、読み取り/書き込み SQL アプリケーションがプライマリ データベースに透過的に再接続できます。 
* **フェールオーバー グループの読み取り/書き込みリスナー**: セカンダリ サーバーの URL を示す **&lt;failover-group-name&gt;.secondary.database.windows.net** という形式の DNS の CNAME レコードです。 これにより、指定した負荷分散規則を使用して、読み取り専用 SQL アプリケーションがセカンダリ データベースに等価的に接続できます。 必要に応じて、セカンダリ サーバーが利用できないときに、読み取り専用トラフィックをプライマリ サーバーに自動的にリダイレクトするように指定できます。
* **自動フェールオーバー ポリシー**: 既定では、フェールオーバー グループは自動フェールオーバー ポリシーを使用して構成されます。 システムは、エラーが検出されるとすぐにフェールオーバーをトリガーします。 アプリケーションからフェールオーバー ワークフローを制御するには、自動フェールオーバーをオフにします。 
* **手動フェールオーバー**: 自動フェールオーバー構成に関係なくいつでも手動でフェールオーバーを開始することができます。 自動フェールオーバー ポリシーが構成されていない場合、フェールオーバー グループ内のデータベースの復元には手動フェールオーバーが必要です。 強制フェールオーバーまたはフレンドリ フェールオーバーを開始できます (データは完全に同期されます)。 後者は、アクティブ サーバーをプライマリ リージョンに再配置する場合に使用できます。 フェールオーバーが完了すると、正しいサーバーに接続されるように DNS レコードが自動的に更新されます。
* **データ消失の猶予期間**: プライマリ データベースとセカンダリ データベースは非同期レプリケーションを使用して同期されるため、フェールオーバーによりデータが消失する場合があります。 アプリケーションのデータ消失の許容範囲を反映するように、自動フェールオーバー ポリシーをカスタマイズできます。 **GracePeriodWithDataLossHours** を構成することで、データ消失につながる可能性があるフェールオーバーの開始までにシステムが待機する時間を制御できます。 

   > [!NOTE]
   > (機能停止がサービス制御面のみに影響するなど) グループ内のデータベースがまだオンラインであることをシステムが検出すると、**GracePeriodWithDataLossHours** に設定した値に関係なく、フェールオーバーを即時にアクティブ化されてデータが完全に同期されます (フレンドリ フェールオーバー)。 この動作によって、復元中のデータ消失が確実に防止されます。 猶予期間はフレンドリ フェールオーバーが使用できない場合にのみ有効になります。 猶予期間が経過する前に機能停止が対処された場合、フェールオーバーはアクティブ化されません。
   >

* **複数のフェールオーバー グループ**: サーバーの同じペアに対して複数のフェールオーバー グループを構成して、フェールオーバーのスケールを制御できます。 各グループは独立してフェールオーバーします。 マルチテナント アプリケーションがエラスティック プールを使用する場合、この機能を使用して各プールのプライマリ データベースとセカンダリ プライマリを混在させることができます。 こうすることで、機能停止の影響をテナントの半分に抑えることができます。

## <a name="best-practices-of-building-highly-available-service"></a>高可用性サービスを構築するうえでのベスト プラクティス

Azure SQL データベースを使った高可用性サービスを構築するには、以下のガイドラインに従う必要があります。

- **フェールオーバー グループの使用**: 1 つまたは複数のフェールオーバー グループを異なるリージョンの 2 つのサーバー (プライマリおよびセカンダリ サーバー) 間に作成できます。 各グループには、プライマリ リージョンに発生した機能停止によりすべてまたは一部のプライマリ データベースが使用できなくなった際にユニットとして復元できる、1 つまたは複数のデータベースを含めることができます。 フェールオーバー グループは、プライマリと同じサービスの目的を共有する geo セカンダリ データベースを作成します。 既にある geo レプリケーションのリレーションシップをこのフェールオーバー グループに追加する場合は、その geo セカンダリが、プライマリと同じサービス レベル目標で構成されていることを確認してください。
- **OLTP ワークロードに読み取り/書き込みのリスナーを使用する**: OLTP 操作を実行するときにサーバーの URL として **&lt;failover-group-name&gt;.database.windows.net** を使用すると、自動的にプライマリに接続されます。 この URL はフェールオーバー後にも変更されません。  
フェールオーバーでは DNS レコードの更新が行われるため、クライアントの接続は、クライアント DNS キャッシュが最新の情報に更新された後にのみ新しいプライマリにリダイレクトされます。
- **読み取り専用ワークロードには読み取り専用リスナーを使用する**: データがある程度古くても構わない、論理的に分離された読み取り専用ワークロードがある場合、アプリケーションでセカンダリ データベースを使用できます。 読み取り専用セッションでは、サーバーの URL として **&lt;failover-group-name&gt;.secondary.database.windows.net** を使用すると、自動的にセカンダリに接続されます。 接続文字列に **ApplicationIntent=ReadOnly** を使用して、読み取りの意図を示すこともお勧めします。 
- **パフォーマンスの低下に備える**: SQL のフェールオーバーの決定は、アプリケーションの他の領域や、使用されている他のサービスとは無関係に行われます。 アプリケーションが、同じリージョン内のコンポーネントや別のリージョン内のコンポーネントと "混在" する場合があります。 パフォーマンスの低下を防ぐために、DR リージョンでアプリケーションのデプロイを冗長化し、この記事のガイドラインに従うようにしてください。  
DR リージョン内のアプリケーションで使う接続文字列を変更する必要はありません。  
- **データの損失に備える**: 機能停止が検出された場合、経験的にデータの損失がゼロであれば、読み取り/書き込みのフェールオーバーが SQL によって自動的にトリガーされます。 それ以外の場合、**GracePeriodWithDataLossHours** に指定された期間、待機状態となります。 **GracePeriodWithDataLossHours** を指定した場合は、データの損失に備えてください。 一般に、機能の停止中、Azure では可用性が重視されます。 データの損失が許容できない場合は、**GracePeriodWithDataLossHours** に設定する値を十分大きく確保してください (24 時間など)。 

> [!IMPORTANT]
> 800 以下の DTU と 250 を超えるデータベースを持ち、geo レプリケーションを使用するエラスティック プールでは、計画されたフェールオーバーに時間がかかったりパフォーマンスが低下したりする問題が発生することがあります。  こうした問題は、geo レプリケーション エンドポイントが地理的に広く分散している場合や、各データベースで複数のセカンダリ エンドポイントが使用されている場合に、書き込みの負荷が高いワークロードで発生しやすくなります。  このような症状は、geo レプリケーションのラグが時間の経過と共に増加するときに見られます。  このラグは、[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) を使用して監視できます。  この問題が発生する場合は、プール DTU の数を増やしたり、同じプール内で geo レプリケーションされるデータベースの数を減らしたりするなどの緩和策があります。

## <a name="upgrading-or-downgrading-a-primary-database"></a>プライマリ データベースのアップグレードまたはダウングレード
セカンダリ データベースの接続を解除することなく、プライマリ データベースを (同じサービス レベル内の) 異なるパフォーマンス レベルにアップグレードまたはダウングレードできます。 アップグレードの場合は、最初にセカンダリ データベースをアップグレードしてからプライマリをアップグレードすることをお勧めします。 ダウングレードは逆の順序で行います。つまり、最初にプライマリをダウングレードしてからセカンダリをダウングレードします。 データベースを異なるサービス レベルにアップグレードまたはダウングレードすると、この推奨事項が強制されます。 

> [!NOTE]
> セカンダリ データベースをフェールオーバー グループ構成の一部として作成した場合、セカンダリ データベースをダウングレードすることは推奨されません。 これは、フェールオーバーがアクティブ化された後にデータ層に通常のワークロードを処理するのに十分な容量を確保するためです。 
>

## <a name="preventing-the-loss-of-critical-data"></a>重要なデータの損失の防止
ワイド エリア ネットワークの遅延は大きいため、連続コピーでは非同期のレプリケーション メカニズムが使用されます。 非同期レプリケーションでは、障害が発生した場合に部分的なデータ損失が避けられません。 しかし、データ損失が許されないアプリケーションもあります。 重要な更新情報を保護するには、アプリケーション開発者は、トランザクションがコミットされた直後に [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) ステム プロシージャを呼び出すことができます。 **sp_wait_for_database_copy_sync** を呼び出すと、最後にコミットされたトランザクションがセカンダリ データベースに転送されるまで、呼び出しスレッドがブロックされます。 ただし、転送されたトランザクションがセカンダリで再生およびコミットされるのを待つことはありません。 **sp_wait_for_database_copy_sync** の対象は、特定の連続コピー リンクです。 プライマリ データベースへの接続権限を持つユーザーが、このプロシージャを呼び出すことができます。

> [!NOTE]
> **sp_wait_for_database_copy_sync** は、フェールオーバー後のデータの損失を防ぎますが、読み取りアクセスに対して完全な同期を保証することはありません。 **sp_wait_for_database_copy_sync** プロシージャ呼び出しによる遅延は、長くなる場合があり、呼び出し時のトランザクション ログのサイズによって異なります。 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>フェールオーバー グループおよびアクティブ geo レプリケーションのプログラムでの管理
前に説明したように、自動フェールオーバー グループ (プレビュー段階) とアクティブ geo レプリケーションは、Azure PowerShell および REST API を使用してプログラムによって管理することもできます。 次の表では、使用できるコマンド セットについて説明します。

**Azure Resource Manager API とロール ベース セキュリティ**: アクティブ geo レプリケーションには、管理のための Azure Resource Manager API 一式 ([Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)、[Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)など) が含まれています。 これらの API は、リソース グループの使用を必要とし、ロール ベース セキュリティ (RBAC) をサポートします。 アクセス ロールの実装方法の詳細については、[Azure のロール ベースのアクセス制御](../active-directory/role-based-access-control-what-is.md)に関するページをご覧ください。

## <a name="manage-sql-database-failover-using-transact-sql"></a>Transact-SQL を使用して SQL データベース フェールオーバーを管理する

| コマンド | [説明] |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |ADD SECONDARY ON SERVER 引数を使用して、既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |FAILOVER または FORCE_FAILOVER_ALLOW_DATA_LOSS を使用して、セカンダリ データベースをプライマリに切り替え、フェールオーバーを開始します |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |REMOVE SECONDARY ON SERVER を使用して、SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。 |
| [sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Azure SQL Database 論理サーバーにある各データベースの、既存のレプリケーション リンクの情報をすべて返します。 |
| [sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |最新のレプリケーション時刻、最後のレプリケーションの遅延、および指定された SQL データベースのレプリケーション リンクに関する他の情報を取得します。 |
| [sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |レプリケーション リンクの状態を含むすべてのデータベース操作の状態が表示されます。 |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |コミットされたすべてのトランザクションがレプリケートされ、アクティブ セカンダリ データベースによって認識されるまで、アプリケーションが待機状態になります。 |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>PowerShell を使用して SQL データベース フェールオーバーを管理する

| コマンドレット | [説明] |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |1 つまたは複数のデータベースを取得します。 |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |セカンダリ データベースをプライマリに切り替えて、フェールオーバーを開始します。 |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。 |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Azure SQL Database とリソース グループまたは SQL Server 間の geo レプリケーション リンクを取得します。 |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   このコマンドはフェールオーバー グループを作成し、それをプライマリとセカンダリの両方のサーバーに登録します。|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | サーバーからフェールオーバー グループを削除し、そのグループが含まれるすべてのセカンダリ データベースを削除します。 |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | フェールオーバー グループ構成を取得します。 |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   フェールオーバー グループの構成を変更します。 |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | フェールオーバー グループのセカンダリ サーバーに対するフェールオーバーをトリガーします。 |
|  | |

> [!IMPORTANT]
> サンプル スクリプトについては、「[PowerShell を使用し、単一 Azure SQL データベースのアクティブ geo レプリケーションを構成する](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)」、「[プールされた Azure SQL データベースのアクティブ geo レプリケーションを PowerShell を使用して構成する](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)」、および「[PowerShell を使用して単一の Azure SQL Database のアクティブ geo レプリケーション フェールオーバー グループを構成する](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)」をご覧ください。
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>REST API を使用して SQL データベース フェールオーバーを管理する
| API | [説明] |
| --- | --- |
| [Create または Update Database (createMode=Restore)](/rest/api/sql/Databases/CreateOrUpdate) |プライマリまたはセカンダリ データベースを作成、更新、または復元します。 |
| [Get Create or Update Database Status](/rest/api/sql/Databases/CreateOrUpdate) |復元操作中にステータスを返します。 |
| [Set Secondary Database as Primary (Planned Failover) (セカンダリ データベースをプライマリとして設定する (計画されたフェールオーバー))](/rest/api/sql/replicationlinks/failover) |現在のプライマリ レプリカのデータベースからフェールオーバーして、どのレプリカのデータベースがプライマリかを設定します。 |
| [Set Secondary Database as Primary (計画されていないフェールオーバー)](/rest/api/sql/replicationlinks/failoverallowdataloss) |現在のプライマリ レプリカのデータベースからフェールオーバーして、どのレプリカのデータベースがプライマリかを設定します。 この操作を行うとデータが失われる可能性があります。 |
| [Get Replication Link](/rest/api/sql/replicationlinks/get) |geo レプリケーション パートナーシップで指定された SQL データベースの特定のレプリケーション リンクを取得します。 sys.geo_replication_links カタログ ビューで表示可能な情報を取得します。 |
| [Replication Links - List By Database](/rest/api/sql/replicationlinks/listbydatabase) | geo レプリケーション パートナーシップで指定された SQL データベースのすべてのレプリケーション リンクを取得します。 sys.geo_replication_links カタログ ビューで表示可能な情報を取得します。 |
| [Delete Replication Link](/rest/api/sql/databases/delete) | データベース レプリケーション リンクを削除します。 フェールオーバー中には実行できません。 |
| [Create or Update Failover Group](/rest/api/sql/failovergroups/createorupdate) | フェールオーバー グループを作成または更新します |
| [Delete Failover Group](/rest/api/sql/failovergroups/delete) | フェールオーバー グループをサーバーから削除します。 |
| [Failover (計画されたフェールオーバー)](/rest/api/sql/failovergroups/failover) | 現在のプライマリ サーバーからこのサーバーにフェールオーバーします。 |
| [Force Failover Allow Data Loss](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |現在のプライマリ サーバーからこのサーバーにフェールオーバーします。 この操作を行うとデータが失われる可能性があります。 |
| [フェールオーバー グループの取得](/rest/api/sql/failovergroups/get) | フェールオーバー グループを取得します。 |
| [List Failover Groups By Server](/rest/api/sql/failovergroups/listbyserver) | サーバーにフェールオーバー グループが表示されます。 |
| [Update Failover Group](/rest/api/sql/failovergroups/update) | フェールオーバー グループを更新します。 |
|  | |

## <a name="next-steps"></a>次の手順
* サンプル スクリプトは、以下を参照してください。
   - [アクティブ geo レプリケーションを使用して、単一のデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [アクティブ geo レプリケーションを使用して、プールされているデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [単一のデータベースのフェールオーバー グループを構成およびフェールオーバーする (プレビュー)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](sql-database-business-continuity.md)を参照してください。
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください。
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
* 新しいプライマリ サーバーとデータベースの認証要件については、 [障害復旧後の SQL Database のセキュリティ](sql-database-geo-replication-security-config.md)に関する記事を参照してください。

