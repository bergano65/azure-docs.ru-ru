---
title: Расширение базы знаний в QnA Maker
description: Улучшить качество вашей базы знаний с активным обучением. Просмотрите, примите или отклоняйте, добавляйте, не удаляя и не изменяя существующие вопросы.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1eb0ed42f700c14350a5e4f1eff9b7592cbf8ef6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474884"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Принять активное обучение предлагаемые вопросы в базе знаний


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Активное обучение изменяет Базу знаний или службу поиска после того, как вы одобрите предложение, а затем сохранить и обучить. Если вы одобрите предложение, оно будет добавлено в качестве альтернативного вопроса.

## <a name="turn-on-active-learning"></a>Включение активного обучения

Для того, чтобы увидеть предложенные вопросы, необходимо [включить активное обучение](use-active-learning.md) для вашего ресурса «NA Maker».

## <a name="view-suggested-questions"></a>Просмотр предлагаемых вопросов

1. Для того, чтобы увидеть предложенные вопросы, на базовой странице **знания edit,** выберите **Параметры представления,** а затем выберите **Показать активные предложения обучения.**

    [![В разделе "Отображивание" портала выберите Show Suggestions, чтобы увидеть новые альтернативы вопросов активного обучения.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Фильтр базы знаний с вопросом и ответом пары, чтобы показать только предложения, выбрав **фильтр предложения**.

    [![Используйте фильтр предложениями переключения для просмотра только активного обучения предлагаемых альтернатив вопрос.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Каждая пара NA предлагает новые альтернативы `✔` вопроса с галочкой, , принять вопрос или `x` отклонить предложения. Установите флажок, чтобы добавить вопрос.

    [![Выберите или отклоните предложенные варианты вопроса активного обучения, выбрав зеленый чековую отметку или красный знак удаления.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Вы можете добавить или удалить _все предложения,_ выбрав **Добавить все** или **отклонить все** в контекстной панели инструментов.

1. Выберите **Save and Train** (Сохранить и обучить), чтобы сохранить изменения в базе знаний.

1. Выберите **Публикацию,** чтобы изменения были доступны на [API GenerateAnswer.](metadata-generateanswer-usage.md#generateanswer-request-configuration)

    При кластеризации 5 или более подобных запросов каждые 30 минут создатель nA предлагает альтернативные вопросы, которые вы должны принять или отклонить.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Архитектурный поток для использования GenerateAnswer и Train AIS от бота

Бот или другое клиентское приложение должно использовать следующий архитектурный поток для активного обучения:

* Бот [получает ответ из базы знаний](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) с Помощью `top` API GenerateAnswer, используя свойство, чтобы получить несколько ответов.
* Бот определяет явную обратную связь:
    * Используя свою [собственную бизнес-логику,](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)отфильтровывайте низкие баллы.
    * В боте или клиент-приложении отобразите список возможных ответов пользователю и получите выбранный пользователь ответ.
* Бот [отправляет выбранный ответ обратно в NA Maker](#bot-framework-sample-code) с [помощью API Train API.](#train-api)


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Используйте верхнее свойство в запросе GenerateAnswer, чтобы получить несколько соответствующих ответов

При отправке вопроса в компании «NA `top` Maker» для получения ответа, свойство тела JSON устанавливает количество ответов для возврата.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Используйте свойство оценки вместе с бизнес-логикой, чтобы получить список ответов, чтобы показать пользователю

Когда клиентское приложение (например, чат-бот) получает ответ, возвращается 3 главных вопроса. Используйте `score` свойство для анализа близости между счетами. Этот диапазон близости определяется вашей собственной бизнес-логикой.

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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Дальнейшие действия клиентского приложения, когда у нескольких вопросов сходные оценки

Клиентское приложение отображает вопросы с возможностью для пользователя, чтобы выбрать _один вопрос,_ который представляет собой большинство представляет их намерения.

Как только пользователь выбирает один из существующих вопросов, клиентское приложение отправляет выбор пользователя в качестве обратной связи с помощью API train API. Эта обратная связь завершает активный цикл обратной связи обучения.

## <a name="train-api"></a>API обучения

Активная обратная связь с обучением отправляется в компании «NA Maker» с запросом Train API POST. Подпись API:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Свойство запроса HTTP|Имя|Тип|Назначение|
|--|--|--|--|
|Параметр маршрута URL|Идентификатор базы знаний|строка|Идентификатор GUID для базы знаний.|
|Пользовательский поддомен|Имя ресурса NAMaker|строка|Название ресурса используется в качестве пользовательского поддона для вашего создателя. Это доступно на странице «Настройки» после публикации базы знаний. Он указан как `host`.|
|Заголовок|Content-Type|строка|Тип носителя текста, отправляемого в API. Значение по умолчанию:`application/json`|
|Заголовок|Авторизация|строка|Ключ конечной точки (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Текст запроса POST|Объект JSON|JSON|Обратная связь с обучением|

Тело JSON имеет несколько настроек:

|Свойство тела JSON|Тип|Назначение|
|--|--|--|--|
|`feedbackRecords`|массиве|Список отзывов.|
|`userId`|строка|Идентификатор пользователя лица, принимающего предложенные вопросы. Формат идентификатора пользователя за вами. Например, адрес электронной почты может быть действительным идентификатором пользователя в вашей архитектуре. Необязательный параметр.|
|`userQuestion`|строка|Точный текст запроса пользователя. Обязательный элемент.|
|`qnaID`|number|ID вопроса, найденный в [ответе GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Пример тела JSON выглядит следующим:

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

Успешный ответ возвращает статус 204 и не JSON ответ тела.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Пакет много записей обратной связи в один вызов

В клиентском приложении, например боте, можно хранить данные, а затем отправлять `feedbackRecords` много записей в одном корпусе JSON в массиве.

Пример тела JSON выглядит следующим:

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

## <a name="bot-framework-sample-code"></a>Код образца фреймворка Bot

Ваш код инфраструктуры бота должен вызывать API Train, если запрос пользователя должен использоваться для активного обучения. Есть две части кода, чтобы написать:

* Определить, следует ли использовать запрос для активного обучения
* Отправка запроса обратно в API поезда создателя nA для активного обучения

В [выборке Azure Bot](https://aka.ms/activelearningsamplebot)обе эти действия были запрограммированы.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Пример кода C для Train API с Bot Framework 4.x

Следующий код иллюстрирует, как отправить информацию обратно в NA Maker с помощью API Train API.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Пример кода Node.js для Train API с Bot Framework 4.x

Следующий код иллюстрирует, как отправить информацию обратно в NA Maker с помощью API Train API.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Активное обучение сохраняется в экспортируемой базе знаний

Когда приложение имеет активную возможность обучения и `SuggestedQuestions` вы экспортируете приложение, столбец в файле tsv сохраняет активные данные обучения.

Столбец `SuggestedQuestions` является объектом JSON информации неявной и `autosuggested`явной `usersuggested` обратной связи. Примером этого объекта JSON для одного вопроса, представленного `help` пользователем, является:

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

Вы также можете использовать API изменений загрузки для просмотра этих изменений, используя REST или любой из языковых SDK:
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Когда вы импортируете это приложение, активное обучение продолжает собирать информацию и рекомендовать предложения для вашей базы знаний.



## <a name="best-practices"></a>Рекомендации

Рекомендации по использованию активного обучения см. в статье [Рекомендации по использованию базы знаний QnA Maker](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Использование метаданных с помощью API GenerateAnswer](metadata-generateanswer-usage.md)
