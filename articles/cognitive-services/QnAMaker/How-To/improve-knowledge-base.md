---
title: 改善知識庫 - QnA Maker
description: 利用主動式學習來改善知識庫的品質。 在不移除或變更現有問題的情況下，進行審查、接受或拒絕、新增。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 88ccbc52e0eb3447d0b99cac9ba41761e292a6fd
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231772"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>接受知識庫中的主動式學習建議問題


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

當您核准建議之後，主動式學習會改變知識庫或搜尋服務，然後儲存並定型。 如果您核准建議，則會將其新增為替代的問題。

## <a name="turn-on-active-learning"></a>開啟主動式學習

若要查看建議的問題，您必須為您的 QnA Maker 資源[開啟主動式學習](use-active-learning.md)。

## <a name="view-suggested-questions"></a>查看建議的問題

1. 若要查看建議的問題，請在 [**編輯**知識庫] 頁面上，選取 [**視圖選項**]，然後選取 [**顯示主動式學習建議**]。

    [![在入口網站的 [編輯] 區段上，選取 [顯示建議]，以查看主動式學習的新問題替代方案。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 藉由選取 [**依建議篩選**]，篩選具有問題和答案配對的知識庫，僅顯示建議。

    [![使用 [依建議篩選] 切換，即可只查看主動式學習的建議問題替代方案。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 每個 QnA 組都會以核取記號來建議新的問題替代專案， `✔` 以接受問題或 `x` 拒絕建議。 選取核取記號可新增問題。

    [![選取或拒絕主動式學習的建議問題，方法是選取綠色核取記號或紅色刪除標記。](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以在內容相關工具列中選取 [全部**新增**] 或 [**全部拒絕**]，來新增或刪除_所有建議_。

1. 選取 [儲存並訓練]****，以儲存對知識庫所做的變更。

1. 選取 [**發佈**]，以允許可從[GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)取得變更。

    當有5個或更多類似的查詢叢集化時，每隔30分鐘 QnA Maker 建議您接受或拒絕的替代問題。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>使用 GenerateAnswer 和從 bot 訓練 Api 的架構流程

Bot 或其他用戶端應用程式應該使用下列架構流程來使用主動式學習：

* Bot 會透過 GenerateAnswer API[從知識庫取得答案](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)，並使用 `top` 屬性來取得一些答案。
* Bot 會決定明確的意見反應：
    * 使用您自己的[自訂商務邏輯](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)，篩選出低分數。
    * 在 bot 或用戶端應用程式中，顯示使用者可能的解答清單，並取得使用者選取的答案。
* Bot 會使用[訓練 API](#train-api)[將選取的回應傳送回 QnA Maker](#bot-framework-sample-code) 。


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>使用 GenerateAnswer 要求中的 top 屬性來取得數個相符的答案

提交問題給 QnA Maker 以取得解答時， `top` JSON 主體的屬性會設定要傳回的答案數目。

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>使用分數屬性和商務邏輯來取得顯示使用者的答案清單

當用戶端應用程式 (例如聊天機器人) 收到回應時，就會傳回前3個問題。 使用 `score` 屬性來分析分數之間的近距離。 這個鄰近範圍是由您自己的商務邏輯所決定。

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>用戶端應用程式在問題具有類似分數時的後續動作

您的用戶端應用程式會顯示問題，並提供選項讓使用者選取最能代表其意圖_的單一問題_。

當使用者選取其中一個現有的問題時，用戶端應用程式會使用 QnA Maker 的訓練 API，將使用者的選擇傳送至意見反應。 此意見反應會完成主動式學習回饋迴圈。

## <a name="train-api"></a>將 API 定型

使用訓練 API POST 要求，將主動式學習意見反應傳送至 QnA Maker。 API 簽章為：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 要求屬性|名稱|類型|目的|
|--|--|--|--|
|URL 路由參數|知識庫識別碼|字串|測試您知識庫的 GUID。|
|自訂子域|QnAMaker 資源名稱|字串|資源名稱會用來做為 QnA Maker 的自訂子域。 這會在您發佈知識庫之後的 [設定] 頁面上提供。 它會列為 `host` 。|
|標頭|Content-Type|字串|傳送至 API 的本文媒體類型。 預設值為：`application/json`|
|標頭|授權|字串|您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|張貼本文|JSON 物件|JSON|訓練意見反應|

JSON 主體有數個設定：

|JSON 主體屬性|類型|目的|
|--|--|--|--|
|`feedbackRecords`|array|意見反應清單。|
|`userId`|字串|接受建議問題之人員的使用者識別碼。 使用者識別碼格式是由您負責。 例如，電子郵件地址可以是您架構中的有效使用者識別碼。 選擇性。|
|`userQuestion`|字串|使用者查詢的確切文字。 必要。|
|`qnaID`|number|問題的識別碼，可在[GenerateAnswer 回應](metadata-generateanswer-usage.md#generateanswer-response-properties)中找到。 |

範例 JSON 主體如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

成功的回應會傳回狀態204，而且不會傳回 JSON 回應主體。

### <a name="batch-many-feedback-records-into-a-single-call"></a>將許多意見反應記錄批次成單一呼叫

在用戶端應用程式（例如 bot）中，您可以儲存資料，然後在陣列中的單一 JSON 主體中傳送許多記錄 `feedbackRecords` 。

範例 JSON 主體如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot framework 範例程式碼

如果使用者的查詢應該用於主動式學習，您的 bot framework 程式碼必須呼叫訓練 API。 有兩段程式碼可以撰寫：

* 判斷查詢是否應該用於主動式學習
* 將查詢傳送回 QnA Maker 的訓練 API 以進行主動式學習

在[Azure Bot 範例](https://github.com/microsoft/BotBuilder-Samples)中，這兩個活動都已經過編寫。

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>使用 Bot Framework 4.x 訓練 API 的範例 c # 程式碼

下列程式碼說明如何使用定型 API 將資訊傳送回 QnA Maker。

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>使用 Bot Framework 4.x 訓練 API 的範例 Node.js 程式碼

下列程式碼說明如何使用定型 API 將資訊傳送回 QnA Maker。

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>主動式學習會儲存在匯出的知識庫中

當您的應用程式啟用主動式學習，並匯出應用程式時， `SuggestedQuestions` tsv 檔案中的資料行會保留使用中的學習資料。

資料 `SuggestedQuestions` 行是隱含、和明確的意見反應之資訊的 JSON 物件 `autosuggested` `usersuggested` 。 針對單一使用者提交的問題，此 JSON 物件的範例 `help` 為：

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

當您重新匯入此應用程式時，主動式學習會繼續收集資訊，並建議您的知識庫建議。



## <a name="best-practices"></a>最佳做法

如需使用主動式學習時的最佳做法，請參閱[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [透過 GenerateAnswer API 使用中繼資料](metadata-generateanswer-usage.md)
