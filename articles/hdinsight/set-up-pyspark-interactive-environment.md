---
title: 使用 Azure HDInsight 工具 PySpark 互動式環境
description: 了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立及提交查詢和指令碼。
keywords: VScode,Azure HDInsight 工具,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,互動式 Hive,互動式查詢
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, tracking-python
ms.date: 04/23/2020
ms.openlocfilehash: ea2bda905879f6bdc6cc515f43474ff570e881b4
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078879"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>設定 Visual Studio Code 的 PySpark 互動式環境

下列步驟示範如何在 VSCode 中設定 PySpark 互動式環境。 此步驟僅適用于非 Windows 使用者。

我們會使用 **python/pip** 命令在您的首頁路徑中建置虛擬環境。 如果想要使用其他版本，您需要手動變更 **python/pip** 命令的預設版本。 請參閱[更新替代項目](https://linux.die.net/man/8/update-alternatives)了解更多詳細資料。

1. 安裝[Python](https://www.python.org/downloads/)和[pip](https://pip.pypa.io/en/stable/installing/)。

   * 從安裝 Python [https://www.python.org/downloads/](https://www.python.org/downloads/) 。 
   * 從安裝 pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) （如果它不是從 Python 安裝安裝）。
   * 使用下列命令來驗證是否已成功安裝 Python 和 pip。 (選用)

        ![檢查 Python pip 版本命令](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > 建議您手動安裝 Python，而不是使用 macOS 預設版本。

2. 執行下列命令來安裝 **virtualenv**。

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>其他套件

在 Linux 上，如果您遇到下列錯誤訊息，請執行下列兩個命令以安裝必要的套件。

   ![安裝適用于 python 的 libkrb5-dev 套件](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

重新開機 VSCode，然後返回 [VSCode 編輯器] 並執行**Spark： PySPark Interactive**命令。

## <a name="next-steps"></a>下一步

### <a name="demo"></a>示範

* HDInsight for VS Code：[影片](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和擴充功能

* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用 Azure Toolkit for IntelliJ 來建立和提交 Apache Spark Scala 應用程式](spark/apache-spark-intellij-tool-plugin.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](spark/apache-spark-jupyter-notebook-install-locally.md)
