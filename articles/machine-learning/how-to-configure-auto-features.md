---
title: AutoML 實驗中的特徵化
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 所提供的特徵化設定，以及自動化 ML 實驗中的功能工程支援方式。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 11bb692027d8a2e5033c7bdaf8eb2c565d1562b0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205697"
---
# <a name="featurization-in-automated-machine-learning"></a>自動化機器學習服務中的特徵化

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本指南中，您將瞭解：

- Azure Machine Learning 提供哪些特徵化設定。
- 如何為您的[自動化機器學習實驗](concept-automated-ml.md)自訂這些功能。

*特色工程*是使用資料的領域知識來建立功能的程式，以協助機器學習服務 (ML) 演算法更進一步瞭解。 在 Azure Machine Learning 中，會套用資料調整和正規化技術，讓特性工程更容易。 這些技術和這項功能工程統稱是*特徵化*在自動化機器學習或*AutoML*實驗中。

本文假設您已經知道如何設定 AutoML 實驗。 如需設定的相關資訊，請參閱下列文章：

- 如需程式碼優先體驗：[使用適用于 Python 的 AZURE MACHINE LEARNING SDK 來設定自動化 ML 實驗](how-to-configure-auto-train.md)。
- 適用于低程式碼或無程式碼的體驗：[使用 Azure Machine Learning Studio 建立、檢查和部署自動化的機器學習模型](how-to-use-automated-ml-for-ml-models.md)。

## <a name="configure-featurization"></a>設定特徵化

在每個自動化機器學習實驗中，預設會將[自動調整和正規化技術](#featurization)套用至您的資料。 這些技術是特徵化的類型，可協助*特定*的演算法對不同尺規上的功能有敏感性。 不過，您也可以啟用其他特徵化，例如*遺漏值的插補*、*編碼*和*轉換*。

> [!NOTE]
> 自動化機器學習特徵化的步驟 (例如功能正規化、處理遺漏的資料，或將文字轉換成數值) 會成為基礎模型的一部分。 當您使用模型進行預測時，會自動將定型期間所套用的相同特徵化步驟套用至您的輸入資料。

針對您使用 Python SDK 設定的實驗，您可以啟用或停用特徵化設定，並進一步指定要用於實驗的特徵化步驟。 如果您使用的是 Azure Machine Learning studio，請參閱[啟用特徵化的步驟](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

下表顯示 `featurization` [AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中已接受的設定：

|特徵化設定 | 描述|
------------- | ------------- |
|`"featurization": 'auto'`| 指定在前置處理過程中，[資料護欄和特徵化步驟](#featurization)會自動完成。 這是預設設定。|
|`"featurization": 'off'`| 指定不會自動執行特徵化步驟。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指定要使用的自訂特徵化步驟。 [了解如何自訂特徵化](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自動特製化

下表摘要說明自動套用至資料的技術。 這些技術適用于使用 SDK 或 studio 設定的實驗。 若要停用此行為，請 `"featurization": 'off'` 在您的物件中設定 `AutoMLConfig` 。

> [!NOTE]
> 如果您打算將 AutoML 建立的模型匯出至[ONNX 模型](concept-onnx.md)，ONNX 格式僅支援以星號表示的特徵化選項 ( "*" ) 。 深入了解[將模型轉換為 ONNX](concept-automated-ml.md#use-with-onnx)。

|特徵化 &nbsp; 步驟| 描述 |
| ------------- | ------------- |
|**捨棄高基數或無變異數功能*** |從定型和驗證集卸載這些功能。 適用于所有遺漏值的功能，在所有資料列中具有相同的值，或具有高基數 (例如，) 的雜湊、識別碼或 Guid。|
|**插補遺漏值*** |對於數值特徵，請插補資料行中值的平均值。<br/><br/>針對類別特徵，請插補最常出現的值。|
|**產生其他功能*** |針對 DateTime 特徵：年、月、日、星期幾、幾月幾日、季、第幾週、小時、分鐘、秒。<br/><br/>針對文字功能：根據 unigrams、bigrams 和 trigrams 的詞彙頻率。|
|**轉換和編碼***|將具有幾個唯一值的數值特徵轉換成類別功能。<br/><br/>一種經常性編碼用於低基數分類功能。 一種經常性雜湊編碼用於高基數類別功能。|
|**字組內嵌**|文字 featurizer 會使用預先定型模型，將文字標記的向量轉換成句子向量。 檔中每個單字的內嵌向量都會與其余部分匯總，以產生檔功能向量。|
|**目標編碼**|針對分類功能，此步驟會將每個類別對應至回歸問題的平均目標值，並針對分類問題的每個類別，將其設為類別的機率。 會套用以頻率為基礎的加權和 k 折迭交叉驗證，以減少稀疏資料類別所造成之對應和雜訊的過度學習。|
|**文字目標編碼**|針對文字輸入，會使用具備文字袋 (bag-of-words) 的堆疊線性模型來產生每個類別其機率。|
|**證據權數 (WoE)**|將 WoE 作為類別資料行相互關聯與目標資料行相互關聯的量值計算。 WoE 是以類別與類別外機率的比率記錄來計算。 此步驟會針對每個類別產生一個數值特徵資料行，而不需要明確插補遺漏值和極端值處理。|
|**叢集距離**|在所有數值資料行上訓練 k 表示叢集模型。 產生*k*個新功能 (每個叢集一個新的數值功能) ，其中包含每個樣本與每個叢集距心的距離。|

## <a name="data-guardrails"></a>資料護欄

*資料護欄*可協助您找出資料 (的潛在問題，例如遺漏值或[類別不平衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)) 。 它們也可協助您採取更正動作來改善結果。

套用資料護欄：

- **針對 SDK 實驗**：當 `"featurization": 'auto'` `validation=auto` 物件中指定了參數或時 `AutoMLConfig` 。
- **針對 studio 實驗**：啟用自動特徵化時。

您可以查看實驗的資料護欄：

- `show_output=True`當您使用 SDK 提交實驗時設定。

- 在 studio 中，在您的自動化 ML 執行的 [**資料護欄**] 索引標籤上。

### <a name="data-guardrail-states"></a>資料 guardrail 狀態

[資料護欄] 會顯示三種狀態的其中一種：

|State| 描述 |
|----|---- |
|**通過**| 未偵測到任何資料問題，您不需要採取任何動作。 |
|**完成**| 已成功將變更套用到資料。 我們鼓勵您審查 AutoML 採取的修正動作，以確保變更與預期的結果一致。 |
|**警示**| 偵測到資料問題，但無法補救。 我們鼓勵您修訂並修正問題。|

### <a name="supported-data-guardrails"></a>支援的資料護欄

下表描述目前支援的資料護欄，以及您在提交實驗時可能會看到的相關狀態：

護欄|狀態|觸發條件&nbsp;&nbsp;
---|---|---
**遺漏特徵值插補** |已通過 <br><br><br> 完成| 在訓練資料中沒有偵測到任何遺漏特徵值。 深入瞭解[缺少-值的插補。](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> 在定型資料中偵測到遺漏的功能值，且已插補。
**高基數特徵處理** |已通過 <br><br><br> 完成| 已分析您的輸入，但未偵測到高基數的功能。 <br><br> 在您的輸入中偵測到高基數的功能，並已處理。
**驗證分割處理** |完成| 驗證設定為 `'auto'` ，而且定型資料包含*少於20000個數據列*。 <br> 定型模型的每個反復專案都是使用交叉驗證來驗證。 深入瞭解[驗證資料](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)。 <br><br> 驗證設定設為 `'auto'` ，定型資料包含超過*20000 個數據列*。 <br> 輸入資料已分割成訓練資料集和驗證資料集，以用於模型驗證。
**類別平衡偵測** |已通過 <br><br><br><br><br> 警示 | 已分析輸入，且訓練資料中所有類別都是平衡的。 如果資料集內的每個類別都有良好的標記法，則會將資料集視為平衡，如樣本的數目和比率所測量。 <br><br><br> 在輸入中偵測到不平衡的類別。 若要修正模型偏差，請修正平衡問題。 深入瞭解[不平衡資料](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)。
**記憶體問題偵測** |已通過 <br><br><br><br> 完成 |<br> 所選取的值 (水準、延隔、滾動視窗) 已分析，而且未偵測到潛在的記憶體不足問題。 深入瞭解時間序列[預測](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)設定。 <br><br><br>選取的值 ([水準]、[延後]、[滾動視窗) 已分析，而且可能會導致您的實驗用盡記憶體。 Lag 或滾動視窗設定已關閉。
**頻率偵測** |已通過 <br><br><br><br> 完成 |<br> 分析時間序列，而且所有資料點都會與偵測到的頻率對齊。 <br> <br> 已分析時間序列，並偵測到與偵測到的頻率不一致的資料點。 這些資料點已從資料集移除。 深入瞭解[時間序列預測的資料準備](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)。

## <a name="customize-featurization"></a>自訂特徵化

您可以自訂特徵化設定，以確保用來將 ML 模型定型的資料和功能會產生相關的預測。

若要自訂 featurizations，請  `"featurization": FeaturizationConfig` 在您的物件中指定 `AutoMLConfig` 。 如果您要使用 Azure Machine Learning studio 進行實驗，請參閱操作[說明文章](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

支援的自訂項目包含：

|自訂|定義|
|--|--|
|**資料行用途更新**|覆寫指定之資料行的功能類型。|
|**轉換器參數更新** |更新指定之轉換器的參數。 目前支援*Imputer* (平均值、最常值和中位數) 和*HashOneHotEncoder*。|
|**卸除資料行** |指定要從特徵化中捨棄的資料行。|
|**區塊轉換器**| 指定要在特徵化進程中使用的區塊轉換器。|

`FeaturizationConfig`使用 API 呼叫來建立物件：

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="next-steps"></a>後續步驟

* 瞭解如何設定自動化 ML 實驗：

    * 如需程式碼優先體驗：[使用 AZURE MACHINE LEARNING SDK 設定自動化 ML 實驗](how-to-configure-auto-train.md)。
    * 針對低程式碼或無程式碼體驗：[在 Azure Machine Learning studio 中建立您的自動化 ML 實驗](how-to-use-automated-ml-for-ml-models.md)。

* 深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

* 深入瞭解[如何使用自動化機器學習來定型回歸模型](tutorial-auto-train-models.md)，或[如何在遠端資源上使用自動化機器學習來定型](how-to-auto-train-remote.md)。
