---
title: "IoT DevKit をクラウドへ: IoT DevKit AZ3166 を Azure IoT Hub に接続する | Microsoft Docs"
description: "このチュートリアルでは、IoT DevKit AZ3166 でのセンサーの状態を監視および視覚化のために Azure IoT Suite に送信する方法を説明します。"
services: iot-hub
documentationcenter: 
author: liydu
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: abaad6514b24bd4bcc923d87a7b721cc80648c47
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-suite-for-remote-monitoring"></a>リモート監視のために IoT DevKit AZ3166 を Azure IoT Suite に接続する

このチュートリアルでは、DevKit でサンプル アプリを実行して、センサー データを Azure IoT Suite に送信する方法を説明します。

## <a name="what-you-need"></a>必要なもの

[ファースト ステップ ガイド](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)に従って以下のことを行います。

* DevKit をWi-Fi に接続
* 開発環境の準備

有効な Azure サブスクリプション 持っていない場合は、次の 2 つの方法のいずれかを使用して登録できます。

* [30 日間の無料試用版 Microsoft Azure アカウント](https://azureinfo.microsoft.com/us-freetrial.html)をアクティブにする
* MSDN または Visual Studio サブスクライバーの場合、[Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を要求する

## <a name="create-an-azure-iot-suite"></a>Azure IoT Suite を作成する

1. [Azure IoT Suite サイト](https://www.azureiotsuite.com/)に移動して、**[新しいソリューションの作成]** をクリックします。
  ![ Azure IoT Suite タイプの選択](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!NOTE]
  > 既定では、このサンプルでは、1 つの IoT Suite が作成された後、S2 IoT Hub が作成されます。 この IoT ハブを膨大な数のデバイスと共に使用しない場合、S2 から S1 にダウングレードして IoT Suite を削除することを強くお勧めします。これにより、必要なくなった際に関連の IoT Hub も削除できます。 

2. **[リモート監視]** を選択します。

3. ソリューション名を入力し、サブスクリプションとリージョンを選択して、**[ソリューションの作成]** をクリックします。 ソリューションのプロビジョニングにはしばらく時間がかかる場合があります。
  ![ソリューションの作成](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. プロビジョニングが完了したら、**[起動]** をクリックします。 一部のシミュレートされたデバイスが、プロビジョニング処理中にソリューション用に作成されます。 **[デバイス]** をクリックしてチェック アウトします。![ダッシュボード](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![コンソール](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. **[デバイスの追加]** をクリックします。

6. **[カスタム デバイス]** で **[新規追加]** をクリックします。
  ![新しいデバイスの追加](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. **[デバイス ID を自分で定義する]** をクリックして、「`AZ3166`」と入力し、**[作成]** をクリックします。
  ![ID を使用したデバイスの作成](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. **IoT Hub ホスト名**をメモし、**[完了]** をクリックします。

## <a name="open-the-remotemonitoring-sample"></a>RemoteMonitoring サンプルを開く

1. 接続されている場合は、コンピューターから DevKit を切断します。

2. VS Code を起動します。

3. DevKit をコンピューターに接続します。 VS Code により DevKit が自動的に検出され、次のページが開きます。
  * DevKit 概要ページ
  * Arduino の例: DevKit の使用を開始するためのハンズオン サンプル

4. 左側の **[Arduino Examples]\(Arduino の例\)** セクションを展開し、**[Examples for MXCHIP AZ3166] > [AzureIoT]** を参照して、**[RemoteMonitoring]** を選択します。 プロジェクト フォルダーを含む新しい VS Code ウィンドウが開きます。
  > [!NOTE]
  > ウィンドウを偶然閉じた場合は、再度開くことができます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino: Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="provision-required-azure-services"></a>必要な Azure サービスのプロビジョニング

ソリューション ウィンドウで表示されたテキスト ボックスに「`task cloud-provision`」と入力し、`Ctrl+P` キー (macOS: `Cmd+P` キー) を使用してタスクを実行します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要な Azure サービスをプロビジョニングできます。

![Azure リソースをプロビジョニングする](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

1. `Ctrl+P` (macOS: `Cmd + P`) を使用し、「**task config-device-connection**」と入力します。

2. ターミナルにより、`task cloud-provision` 手順から取得される接続文字列を使用するかどうか確認するメッセージが表示されます。 [新規作成...] をクリックして、独自のデバイスの接続文字列を入力することもできます。

3. ターミナルによって、構成モードを開始するよう求められます。 これを行うには、A ボタンを押しながら、リセット ボタンを押して離します。 画面に、DevKit の ID と "構成" が表示されます。
  ![接続文字列の入力](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. `task config-device-connection` が完了したら、`F1` をクリックして VS Code コマンドをロードして `Arduino: Upload` を選択すると、VS Code によって Arduino スケッチの確認とアップロードが開始されます。![Arduino スケッチの確認とアップロード](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit が再起動され、コードの実行が開始されます。

## <a name="test-the-project"></a>プロジェクトのテスト

サンプル アプリの実行時に、DevKit によって Wi-Fi 経由でセンサー データが Azure IoT Suite に送信されます。 結果を表示するには、次の手順に従います。

1. Azure IoT Suite に移動し、**[ダッシュボード]** をクリックします。

2. Azure IoT Suite ソリューション コンソールに DevKit センサーの状態が表示されます。

![Azure IoT Suite でのセンサー データ](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>デバイス ID の変更

IoT Hub でデバイス ID を変更するには、[このガイド](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)に従います。 ハードコーディングされた **AZ3166** をコード内のカスタマイズされたデバイス ID に変更する必要がある場合、 [ここで](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23)コード行を変更できます。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を参照するか、以下のチャネルからお問い合わせください。

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

ここでは、DevKit デバイスを Azure IoT Suite に接続して、センサー デバイスを視覚化する方法を説明しました。推奨する次の手順は、以下のとおりです。

* [Azure IoT Suite の概要](https://docs.microsoft.com/azure/iot-suite/)
