---
title: "スクリプト アクション - Azure HDInsight の Jupyter で Python パッケージをインストールする | Microsoft Docs"
description: "スクリプト アクションを使用して HDInsight の Spark clusters で Jupyter notebooks を構成し、外部の Python パッケージを使用する方法に関する詳細な手順。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: e9983cb29c59ebf29dd247c2e6f70cc052eab39d
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2018
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>スクリプト アクションを使用して HDInsight の Apache Spark クラスターの Jupyter Notebook で外部の Python パッケージをインストールする
> [!div class="op_single_selector"]
> * [cell magic の使用](apache-spark-jupyter-notebook-use-external-packages.md)
> * [スクリプト アクションの使用](apache-spark-python-package-installation.md)
>
>

スクリプト アクションを使用して、HDInsight (Linux) 上の Apache Spark クラスターを、そのクラスターの標準では搭載されていない外部のコミュニティから提供されている **Python** パッケージを使用するよう構成する方法について説明します。

> [!NOTE]
> `%%configure` マジックを使用して Jupyter Notebook を構成して外部パッケージを使用することもできます。 手順については、「[HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)」を参照してください。
> 
> 

[パッケージ インデックス](https://pypi.python.org/pypi)で、利用できるすべてのパッケージを検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、[Anaconda](https://docs.continuum.io/anaconda/pkg-docs) または [conda-forge](https://conda-forge.org/feedstocks/) で使用できるパッケージをインストールできます。

この記事では、クラスターでスクリプト アクションを使用して [TensorFlow](https://www.tensorflow.org/) パッケージをインストールし、Jupyter Notebook で使用する方法について説明します。

## <a name="prerequisites"></a>前提条件
次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

   > [!NOTE]
   > HDInsight Linux に Spark クラスターがない場合は、クラスターの作成時にスクリプト アクションを実行できます。 [カスタム スクリプト アクションの使用方法](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)に関するドキュメントを参照してください。
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter Notebook で外部のパッケージを使用する

1. [Azure Portal](https://portal.azure.com/) のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、左側のウィンドウの **[スクリプト アクション]** をクリックします。 ヘッド ノードと worker ノードに TensorFlow をインストールするカスタム アクションを実行します。 Bash スクリプトは https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh から参照できます。[カスタム スクリプト アクションの使用方法](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)に関するドキュメントを参照してください。

   > [!NOTE]
   > クラスターには 2 つの Python インストールがあります。 Spark では、`/usr/bin/anaconda/bin` の Anaconda Python インストールが使用されます。 そのインストールは、`/usr/bin/anaconda/bin/pip` および `/usr/bin/anaconda/bin/conda` を介してカスタム アクションで参照してください。
   > 
   > 

3. PySpark Jupyter Notebook を開きます

    ![新しい Jupyter Notebook の作成](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "新しい Jupyter Notebook の作成")

4. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。

    ![Notebook の名前を指定](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Notebook の名前を指定")

5. 次に `import tensorflow` によって、hello world の例を実行します。 

    コピーするコード:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    結果は次のようになります。
    
    ![TensorFlow コード実行](./media/apache-spark-python-package-installation/execution.png "TensorFlow コードの実行")

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
