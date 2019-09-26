---
title: Расширение базы знаний в QnA Maker
titleSuffix: Azure Cognitive Services
description: Активное обучение позволяет улучшить качество базы знаний, предлагая на основе пользовательских отзывов альтернативные вопросы для пар существующих вопросов и ответов. Проверенные предложения можно добавить к существующим вопросам или отклонить. База знаний не изменяется автоматически. Чтобы изменения вступили в силу, необходимо принять предложения. Так вы добавите новые вопросы, не меняя и не удаляя существующие.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: ab4447c8c07f8e8315c0258cc3254e5272ab7582
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272441"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Использование активного обучения для расширения базы знаний

Активное обучение позволяет улучшить качество базы знаний, предлагая на основе пользовательских отзывов альтернативные вопросы для пар существующих вопросов и ответов. Проверенные предложения можно добавить к существующим вопросам или отклонить. 

База знаний не изменяется автоматически. Чтобы изменения вступили в силу, необходимо принять предложения. Так вы добавите новые вопросы, не меняя и не удаляя существующие.

## <a name="what-is-active-learning"></a>Что такое активное обучение?

QnA Maker изучает новые варианты вопросов с помощью механизма явных и неявных отзывов.
 
* [Неявная обратная связь](#how-qna-makers-implicit-feedback-works) — ранжирование понимает, когда вопрос пользователя имеет несколько ответов с оценками, которые очень близки к этому, и рассматривает это как отзыв. Для этого не нужно ничего делать.
* [Явная обратная связь](#how-you-give-explicit-feedback-with-the-train-api) . когда из базы знаний возвращаются несколько ответов с небольшим изменением, клиентское приложение запрашивает у пользователя вопрос о том, какой именно вопрос является верным. Явная обратная связь пользователя отправляется в QnA Maker с помощью [API обучения](#train-api). 

Оба метода предоставляют ранжирование с аналогичными запросами, которые являются кластеризованными.

## <a name="how-active-learning-works"></a>Принцип работы активного обучения

Активное обучение активируется на основе оценок первых нескольких ответов, возвращенных QnA Maker. Если различия оценки находятся в небольшом диапазоне, запрос считается возможным (в качестве альтернативного вопроса) для каждой из возможных пар QnA. После принятия предлагаемого вопроса для определенной пары QnA она отклоняется для других пар. После принятия предложений необходимо забывать о сохранении и обучении.

Активное обучение предоставляет наилучшие возможные предложения, если конечные точки получают приемлемое количество разных запросов использования. Если пять или более похожих запросов являются кластеризованными, каждые 30 минут QnA Maker предлагает пользователю пользовательские вопросы в конструкторе базы знаний для принятия или отклонения. Все предложения группируются по сходству, и лучшие предложения для альтернативных вопросов отображаются с учетом частоты конкретных запросов пользователей.

После предложения вопросов на портале QnA Maker необходимо просмотреть и принять или отклонить эти предложения. Нет API для управления предложениями.

## <a name="how-qna-makers-implicit-feedback-works"></a>Как работают неявные Отзывы QnA Maker

Неявная обратная связь QnA Maker использует алгоритм для определения сходства очков, а затем делает активными предложениями по обучению. Для определения сходства оценок применяется сложный алгоритм. Диапазоны в следующем примере не предназначены для фиксирования, но их следует использовать в качестве рекомендации для понимания воздействия только алгоритма.

Если вопрос получает оценку с высокой степенью достоверности (например, 80 %), диапазон оценок, используемых для активного обучения, расширяется (приблизительно на 10 %). Если оценка достоверности снижается (например, до 40 %), диапазон оценок также сокращается (приблизительно на 4 %). 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Как вы предоставляете явный отзыв с помощью API обучения

Важно, чтобы QnA Maker получает явный отзыв о том, какой из ответов был лучшим ответом. Способ определения наилучшего ответа зависит от вас и может включать:

* Отзывы пользователей, выбрав один из ответов.
* Бизнес-логика, например определение допустимого диапазона оценок.  
* Сочетание отзывов пользователей и бизнес-логики.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Обновление версии среды выполнения для использования активного обучения

Активное обучение поддерживается в среде выполнения версии 4.4.0 и выше. Если ваша база знаний создана с помощью более ранней версии, [обновите среду выполнения](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates), чтобы воспользоваться этой функцией. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Включение активного обучения для просмотра предложений

По умолчанию активное обучение отключено. Чтобы просматривать предлагаемые вопросы, включите эту функцию. После включения активного обучения необходимо отправить сведения из клиентского приложения в QnA Maker. Дополнительные сведения см. в разделе [архитектурный поток для использования женератеансвер и обучение API-интерфейсов из Bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Выберите **опубликовать** , чтобы опубликовать базу знаний. Активные запросы на обучение собираются только из конечной точки прогнозирования API Женератеансвер. Запросы к области тест на портале QnA Maker не влияют на активное обучение.

1. Чтобы включить активное обучение на портале QnA Maker, перейдите в правый верхний угол, выберите свое **имя**и перейдите к [**параметрам службы**](https://www.qnamaker.ai/UserSettings).  

    ![Включить альтернативные варианты вопросов активного обучения на странице "Параметры службы". Выберите имя пользователя в верхнем правом меню, а затем выберите параметры службы.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Найдите службу QnA Maker, а затем активируйте переключатель **Active Learning** (Активное обучение). 

    [![На странице Параметры службы включите функцию активного обучения. Если вы не можете переключить эту функцию, возможно, потребуется обновить службу.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Точная версия на предыдущем рисунке показана только в качестве примера. Ваша версия может быть другой. 

    После включения **активного обучения** база знаний предлагает новые вопросы через регулярные интервалы в соответствии с вопросами, отправленными пользователем. Вы можете отключить **активное обучение** с помощью этого же переключателя.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Принятие активного предложения по обучению в базе знаний

Активное обучение изменяет базу знаний или Служба поиска после утверждения предложения, а затем сохраняет и обучить. Если вы утвердите предложение, оно будет добавлено в качестве альтернативного вопроса.

1. Чтобы просмотреть предлагаемые вопросы, на странице « **изменение** базы знаний» выберите **Просмотр параметров**, а затем выберите **Показать активные предложения по обучению**. 

    [![В разделе "Правка" на портале выберите Показать предложения, чтобы просмотреть альтернативы нового вопроса активного обучения.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Отфильтруйте базу знаний с помощью пар вопросов и ответов, чтобы отображались только предложения, выбрав **Фильтр по предложениям**.

    [![Используйте переключатель фильтровать по предложениям для просмотра только альтернативных вариантов вопроса, предлагаемых в активном обучении.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Каждая пара QnA предлагает новые альтернативные варианты вопроса с галочкой `✔` ,, чтобы принять вопрос `x` или отклонить предложения. Установите флажок, чтобы добавить вопрос. 

    [![Выберите или отклоните альтернативные варианты вопроса активного обучения, выбрав зеленую галочку или красную метку удаления.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Вы можете добавить или удалить _все предложения_ , выбрав **Добавить все** или **Отклонить все** на контекстной панели инструментов.

1. Выберите **Save and Train** (Сохранить и обучить), чтобы сохранить изменения в базе знаний.

1. Выберите **опубликовать** , чтобы разрешить доступ к изменениям из [API женератеансвер](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Если пять или более похожих запросов являются кластеризованными, каждые 30 минут QnA Maker предлагает дополнительные вопросы, которые можно принять или отклонить.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Архитектурный поток для использования Женератеансвер и обучения API-интерфейсов из Bot

Для использования активного обучения в роботе или другом клиентском приложении следует использовать следующую архитектурную последовательность:

* Bot [получает ответ от базы знаний](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) с помощью API женератеансвер, используя `top` свойство для получения ряда ответов.
* Bot определяет явный отзыв:
    * Используя собственную [бизнес-логику](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), отфильтруйте низкие оценки.
    * В окне "bot" или "клиент-приложение" отобразится список возможных ответов для пользователя и получите выбранный ответ.
* Bot [отправляет выбранный ответ обратно в QnA Maker](#bot-framework-sample-code) с помощью [API обучения](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Использование свойства Top в запросе Женератеансвер для получения нескольких соответствующих ответов

При отправке вопроса в QnA Maker для ответа `top` свойство тела JSON задает число возвращаемых ответов. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Использование свойства Score вместе с бизнес-логикой для получения списка ответов для отображения пользователя

Когда клиентское приложение (например, Bot чата) получает ответ, возвращаются 3 основных вопроса. Используйте свойство `score` , чтобы проанализировать близость между показателями. Этот диапазон сходства определяется собственной бизнес-логикой. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

Клиентское приложение отображает вопросы с возможностью выбора _одного вопроса_ , который наиболее соответствует намерениям. 

Когда пользователь выбирает один из существующих вопросов, клиентское приложение отправляет выбор пользователя в качестве отзыва с помощью API обучения QnA Maker. Этот отзыв завершает активный цикл обратной связи. 

## <a name="train-api"></a>API обучения

Активная обратная связь отправляется в QnA Maker с запросом на обучение API POST. Сигнатура API:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Свойство HTTP-запроса|Название|Type|Цель|
|--|--|--|--|
|Параметр URL-маршрута|Идентификатор базы знаний|строка|Идентификатор GUID для базы знаний.|
|Дочерний домен узла|Имя ресурса QnAMaker|строка|Имя узла для QnA Maker в подписке Azure. Это можно найти на странице параметры после публикации базы знаний. |
|Header|Content-Type|строка|Тип носителя текста, отправляемого в API. Значение по умолчанию:`application/json`|
|Header|Authorization|строка|Ключ конечной точки (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Текст запроса POST|Объект JSON|JSON|Отзыв по обучению|

Тело JSON имеет несколько параметров:

|Свойство тела JSON|Type|Цель|
|--|--|--|--|
|`feedbackRecords`|array|Список отзывов.|
|`userId`|строка|Идентификатор пользователя, который принимает предлагаемые вопросы. Пользователь должен иметь формат идентификатора пользователя. Например, адрес электронной почты может быть допустимым ИДЕНТИФИКАТОРом пользователя в вашей архитектуре. Необязательный элемент.|
|`userQuestion`|строка|Точный текст запроса пользователя. Обязательный элемент.|
|`qnaID`|number|Идентификатор вопроса, найденный в [ответе женератеансвер](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Пример текста JSON выглядит следующим образом:

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

Успешный ответ возвращает состояние 204 и не имеет текста ответа JSON. 

### <a name="batch-many-feedback-records-into-a-single-call"></a>Пакетная обработка большого количества записей отзывов в один вызов

В клиентском приложении, таком как Bot, можно сохранить данные, а затем отправить много записей в одном тексте JSON в `feedbackRecords` массиве. 

Пример текста JSON выглядит следующим образом:

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

## <a name="bot-framework-sample-code"></a>Пример кода для платформы Bot

Код платформы Bot должен вызывать API обучения, если запрос пользователя должен использоваться для активного обучения. Написание кода состоит из двух частей:

* Определение необходимости использования запроса для активного обучения
* Отправить запрос в API обучения QnA Maker для активного обучения

В [примере Azure Bot](https://aka.ms/activelearningsamplebot)были запрограммированы оба действия. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Пример C# кода для обучения API с помощью Bot Framework 4. x

В следующем примере кода показано, как отправить сведения обратно в QnA Maker с помощью API обучения. Этот [полный пример кода](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) доступен на сайте GitHub.

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Пример кода Node. js для обучения API с помощью Bot Framework 4. x 

В следующем примере кода показано, как отправить сведения обратно в QnA Maker с помощью API обучения. Этот [полный пример кода](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) доступен на сайте GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Активное обучение сохраняется в экспортированной базе знаний

Если в приложении включено активное обучение и вы экспортируете приложение, `SuggestedQuestions` столбец в TSV файле содержит активные обучающие данные. 

Столбец представляет собой объект JSON сведений о `autosuggested`неявных, и явных, `usersuggested` обратных отзывах. `SuggestedQuestions` Пример этого объекта JSON для одного отправленного пользователем вопроса `help` :

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

Вы также можете использовать API скачивания изменений для просмотра этих изменений с помощью функций RESTFUL или любого из пакетов SDK на основе языка:
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


При повторном импорте этого приложения активное обучение продолжит собираются сведения и рекомендации для своей базы знаний. 



## <a name="best-practices"></a>Рекомендации

Рекомендации по использованию активного обучения см. в статье [Рекомендации по использованию базы знаний QnA Maker](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Следующие шаги
 
> [!div class="nextstepaction"]
> [Использование метаданных с API Женератеансвер](metadata-generateanswer-usage.md)
