---
title: Azure Durable Functions 單元測試
description: 深入了解如何針對 Durable Functions 進行單元測試。
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74231233"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions 單元測試

單元測試是現代軟體開發實務中很重要的一部分。 單元測試會驗證商務邏輯行為，並且防止在未來引進未注意到的中斷性變更。 Durable Functions 很容易變得複雜，所以引進單元測試有助於避免中斷性變更。 下列各節說明如何對三個函數類型進行單元測試：協調流程用戶端、協調器和活動函式。

> [!NOTE]
> 本文提供以 Durable Functions 1.x 為目標之 Durable Functions 應用程式單元測試的指導方針。 尚未更新，以將 Durable Functions 2.x 中引進的變更納入考慮。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

## <a name="prerequisites"></a>必要條件

這篇文章中的範例需要下列概念和架構的知識：

* 單元測試

* 長期函式

* [xUnit](https://xunit.github.io/) - 測試架構

* [moq](https://github.com/moq/moq4) - 模擬架構

## <a name="base-classes-for-mocking"></a>模擬的基底類別

Durable Functions 1.x 中的三個抽象類別都支援模擬：

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

這些類別是 `DurableOrchestrationClient` 、和的基類，這些類別會 `DurableOrchestrationContext` `DurableActivityContext` 定義協調流程用戶端、協調器和活動方法。 模擬會設定基底類別方法的預期行為，讓單元測試可以驗證商務邏輯。 有一個雙步驟工作流程可以針對協調流程用戶端和協調器中的商務邏輯進行單元測試：

1. 定義協調流程用戶端和協調器函式簽章時，請使用基類，而不是具體的執行。
2. 在單元測試中模擬基底類別的行為，並且驗證商務邏輯。

在下列段落中尋找測試函式的詳細資料，這些函式使用協調流程用戶端繫結和協調器觸發程序繫結。

## <a name="unit-testing-trigger-functions"></a>針對觸發程序函式進行單元測試

在本節中，單元測試會驗證下列 HTTP 觸發程序函式對於啟動新協調流程的邏輯。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

單元測試工作會驗證回應承載中提供的 `Retry-After` 標頭值。 因此，單元測試會模擬一些 `DurableOrchestrationClientBase` 方法，以確保可預測的行為。

首先，需要基類的 mock `DurableOrchestrationClientBase` 。 Mock 可以是執行的新類別 `DurableOrchestrationClientBase` 。 不過，使用例如 [moq](https://github.com/moq/moq4) 的模擬架構可以簡化程序：

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

然後模擬 `StartNewAsync` 方法以傳回已知的執行個體識別碼。

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

接下來模擬 `CreateCheckStatusResponse` 以一律傳回空的 HTTP 200 回應。

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

也會模擬 `ILogger`：

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

現在 `Run` 方法是從單元測試呼叫：

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        loggerMock.Object);
 ```

 最後一個步驟是比較輸出與預期值：

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

合併所有步驟之後，單元測試會有下列程式碼：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>針對協調器函式進行單元測試

針對協調器函式進行單元測試更加有趣，因為它們通常有更多商務邏輯。

在本節中，單元測試會驗證 `E1_HelloSequence` 協調器函式的輸出：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

單元測試程式碼會從建立模擬開始：

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

然後會模擬活動方法呼叫：

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

接下來，單元測試會呼叫 `HelloSequence.Run` 方法：

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

最後會驗證輸出：

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

合併所有步驟之後，單元測試會有下列程式碼：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>針對活動函式進行單元測試

可以使用與非持久函式相同的方式來針對活動函式進行單元測試。

在本節中，單元測試會驗證 `E1_SayHello` 活動函式的行為：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

且單元測試會驗證輸出的格式。 單元測試可以直接使用參數類型或 mock `DurableActivityContextBase` 類別：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [深入了解 moq](https://github.com/Moq/moq4/wiki/Quickstart)
