---
title: Расширение базы знаний в QnA Maker
titleSuffix: Azure Cognitive Services
description: Активное обучение позволяет улучшить качество базы знаний, предлагая на основе пользовательских отзывов альтернативные вопросы для пар существующих вопросов и ответов. Проверенные предложения можно добавить к существующим вопросам или отклонить. База знаний не изменяется автоматически. Необходимо принять предложения для изменения вступили в силу. Так вы добавите новые вопросы, не меняя и не удаляя существующие.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/06/2019
ms.author: diberry
ms.openlocfilehash: f8d2f6d9fce6a249a782f959ac7672ac8e123fbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075163"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Использование активного обучения для повышения знаний

Активное обучение позволяет улучшить качество базы знаний, предлагая на основе пользовательских отзывов альтернативные вопросы для пар существующих вопросов и ответов. Проверенные предложения можно добавить к существующим вопросам или отклонить. 

База знаний не изменяется автоматически. Чтобы изменения вступили в силу необходимо принять предложения. Так вы добавите новые вопросы, не меняя и не удаляя существующие.

## <a name="what-is-active-learning"></a>Что такое активное обучение?

QnA Maker изучает новые варианты вопросов с помощью механизма явных и неявных отзывов.
 
* [Неявные отзыв](#how-qna-makers-implicit-feedback-works) — средства ранжирования понимает, когда пользователь вопрос имеет несколько ответов с оценки, которые очень близки и рассматривает обратной связи. Не нужно ничего делать это.
* [Явные отзыв](#how-you-give-explicit-feedback-with-the-train-api) — когда несколько ответов с небольшом в результаты возвращаются из базы знаний, клиентское приложение запрашивает у пользователя какие вопрос является правильным вопросом. Явные отзыв пользователя отправляется QnA Maker с [Train API](#train-api). 

Оба метода предоставляют средства ранжирования с сходных запросов, кластеризованы.

## <a name="how-active-learning-works"></a>Принцип работы активного обучения

Активное обучение активируется на основе этой оценки лучшие несколько ответы, возвращаемые QnA Maker. Если оценка различия находятся в пределах небольшого диапазона, запрос считается возможные предложения (как альтернативный вопрос) для каждого из возможных пар QnA. После принятия предложения предлагаемые вопрос для определенной пары QnA, оно отклоняется для других пар. Необходимо помнить сохранить и обучения, после принятия предложения.

Активное обучение предоставляет наилучшие возможные предложения, если конечные точки получают приемлемое количество разных запросов использования. Когда кластеризованы 5 или больше подобных запросов, каждые 30 минут, QnA Maker предлагает вопросы пользователей в конструктор базы знаний, чтобы утвердить или отклонить. Все предложения группируются по сходству, и лучшие предложения для альтернативных вопросов отображаются с учетом частоты конкретных запросов пользователей.

После вопросы предлагаются на портале QnA Maker, необходимо просмотреть и принять или отклонить эти предложения. Не существует API для управления предложения.

## <a name="how-qna-makers-implicit-feedback-works"></a>Как работает отзыв неявное QnA Maker

Неявные отзыв QnA Maker использует алгоритм определить оценку с учетом расположения, а затем предложения активное обучение. Для определения сходства оценок применяется сложный алгоритм. Диапазоны в следующем примере не предназначены для исправлена, но следует использовать в качестве структуру, чтобы понять влияние только алгоритм.

Если вопрос получает оценку с высокой степенью достоверности (например, 80 %), диапазон оценок, используемых для активного обучения, расширяется (приблизительно на 10 %). Если оценка достоверности снижается (например, до 40 %), диапазон оценок также сокращается (приблизительно на 4 %). 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Как вы отзыв явные с помощью API обучение

Очень важно, что QnA Maker получает явные отзывы о том, какие из ответов была лучший ответ. Как определяется лучший ответ зависит от пользователя и могут включать:

* Отзывы пользователей, выбрав один из ответов.
* Бизнес-логику, например для определения допустимые оценки диапазона.  
* Сочетание обоих пользователя обратной связи и бизнес-логики.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Обновить версию среды выполнения для использования активное обучение

Активное обучение поддерживается в среде выполнения версии 4.4.0 и выше. Если ваша база знаний создана с помощью более ранней версии, [обновите среду выполнения](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates), чтобы воспользоваться этой функцией. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Включите active обучения для просмотра предложений

По умолчанию активное обучение отключено. Чтобы просматривать предлагаемые вопросы, включите эту функцию. После включения активное обучение, нужно отправить сведения из клиентского приложения к QnA Maker. Дополнительные сведения см. в разделе [архитектуры потока по использованию GenerateAnswer и API-интерфейсы обучения из программы-робота,](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Выберите **публикации** Чтобы опубликовать базу знаний. Активное обучение запросы собираются из только конечная точка GenerateAnswer API прогноза. Запросы к области теста на портале QnA Maker не влияют на активное обучение.

1. Чтобы включить active обучения на портале QnA Maker, перейдите на верхний правый угол, выберите ваш **имя**, перейдите в меню [ **параметры службы**](https://www.qnamaker.ai/UserSettings).  

    ![Включение активное обучение возможных альтернатив вопрос, на странице параметров службы. Выберите свое имя пользователя в правом верхнем меню, а затем выберите параметры службы.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Найдите службу QnA Maker, а затем активируйте переключатель **Active Learning** (Активное обучение). 

    [![На странице параметров службы Переключите режим активное обучение. Если вы не сможете переключить функцию, может потребоваться обновление службы.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Один раз **активное Обучение** — включена, знаний предлагает новые вопросы с регулярными интервалами, судя по вопросам, отправленные пользователем. Вы можете отключить **активное обучение** с помощью этого же переключателя.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Примите предложение активное обучение в базе знаний

1. Чтобы увидеть предлагаемые вопросы, на **изменить** знаний странице выберите **параметры представления**, а затем выберите **Показывать варианты активное Обучение**. 

    [![В разделе "Изменить" на портале выберите Показать предложения, чтобы увидеть новый вопрос альтернативы активное обучение.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Фильтровать базы знаний с парами вопросов и ответов, чтобы показывать только варианты, выбрав **фильтр по предложения**.

    [![Используйте фильтр по переключателю предложения для просмотра только активное обучение предлагаемые вопрос альтернатив.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Каждая пара QnA предлагает новые варианты вопрос с флажком, `✔` , чтобы принять вопрос или `x` для отклонения предложения. Установите флажок, чтобы добавить вопрос. 

    [![Выберите или отклонить альтернативы активное обучение предлагаемые вопрос, выбрав зелеными флажками или красным удаления метки.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Можно добавлять и удалять _все предложения_ , выбрав **добавьте все** или **Отклонить все** контекстные панели инструментов.

1. Выберите **Save and Train** (Сохранить и обучить), чтобы сохранить изменения в базе знаний.

1. Выберите **публикации** разрешить внесение изменений, будут отображаться на [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Когда кластеризованы 5 или больше подобных запросов, каждые 30 минут, QnA Maker предлагает альтернативный вопросы можно принять или отклонить.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Архитектурные потока по использованию GenerateAnswer и API-интерфейсы обучения из программы-робота

Программы-робота или другого клиентского приложения должны использовать следующий поток архитектуры использование активного обучения:

* Bot [получает ответ из базы знаний](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) с GenerateAnswer API, с помощью `top` свойство для получения числа ответов.
* Bot определяет явные обратной связи:
    * С помощью собственных [пользовательской бизнес-логики](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), отфильтровать низкой оценки.
    * В программ-роботов или клиентского приложения отображения списка возможных вариантов ответа для пользователя и получить выбранный ответ пользователя.
* Bot [отправляет выбранный ответ обратно на QnA Maker](#bot-framework-sample-code) с [Train API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Используйте свойство top в запросе GenerateAnswer, чтобы получить несколько соответствующие ответы

При отправке вопрос, ответ, чтобы QnA Maker `top` число ответов для возврата задает свойство текста JSON. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Используйте свойство оценка вместе с бизнес-логику для получения списка ответов показать пользователю

Когда клиентское приложение (например, чатбот) получает ответ, возвращаются самые популярные вопросы об 3. Используйте `score` свойство для анализа расстояния между оценок. Этот диапазон с учетом расположения, определяется собственную бизнес-логику. 

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

Клиентское приложение отображает вопросы с возможность пользователю выбрать _один вопрос_ , чтобы большинство представляет их намерений. 

Когда пользователь выбирает один из существующих вопросов, клиентское приложение отправляет выбора пользователя с помощью API службы QnA Maker Train обратной связи. Ваши отзывы завершения активного обучения цикл обратной связи. 

## <a name="train-api"></a>Обучение API

Активное обучение отзыв отправляется QnA Maker вместе с запросом Train API POST. Подпись API является:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Свойство запроса HTTP|ИМЯ|type|Назначение|
|--|--|--|--|
|Параметр URL-адреса маршрута|Идентификатор базы знаний|string|Идентификатор GUID для базы знаний.|
|Узла поддомен|Имя ресурса QnAMaker|string|Имя узла для вашей службы QnA Maker в вашей подписке Azure. Доступно на странице "Параметры" после ее публикации в базе знаний. |
|Верхний колонтитул|Content-Type|string|Тип носителя текста, отправляемого в API. Значение по умолчанию —: `application/json`|
|Верхний колонтитул|Авторизация|string|Ключ конечной точки (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Текст запроса POST|Объект JSON|JSON|Отзыв обучения|

Текст JSON имеет несколько параметров:

|Свойство текста JSON|type|Назначение|
|--|--|--|--|
|`feedbackRecords`|массив|Список отзыва.|
|`userId`|string|Идентификатор лица, принимая на предложенные вопросы. Формат идентификатора пользователя зависит от пользователя. Например адрес электронной почты может быть допустимый идентификатор пользователя в архитектуре. Необязательный элемент.|
|`userQuestion`|string|Точный текст вопроса. Обязательный элемент.|
|`qnaID`|номер|Идентификатор вопроса, найден в [GenerateAnswer ответа](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Пример текста JSON выглядит как:

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

Успешный ответ возвращается в состояние 204 и нет текста ответа JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Пример кода Bot framework

Ваш код bot framework должно вызвать Train API, если запрос пользователя следует использовать для активное обучение. Существует два фрагмента кода для записи.

* Определить, если запрос должен использоваться для активное обучение
* Отправить запрос к API службы QnA Maker обучение для активное обучение

В [образец программ-роботов Azure](https://aka.ms/activelearningsamplebot), оба эти действия запрограммированы. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Пример C# кода для API обучение с помощью Bot Framework 4.x

Следующий код иллюстрирует способ обратной отправки сведения QnA Maker, с помощью Train API. Это [полный пример кода](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) доступен на сайте GitHub.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Пример кода Node.js для API обучение с помощью Bot Framework 4.x 

Следующий код иллюстрирует способ обратной отправки сведения QnA Maker, с помощью Train API. Это [полный пример кода](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) доступен на сайте GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Активное обучение сохранен экспортированный базы знаний

Если приложение имеет активное обучение включена, и экспортировать приложение, `SuggestedQuestions` столбца в файле tsv сохраняет данные активное обучение. 

`SuggestedQuestions` Столбец представляет собой объект JSON, сведения о неявных, `autosuggested`и явным, `usersuggested` обратной связи. Пример объекта JSON для один пользователь отправил вопрос `help` является:

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

При повторном импорте это приложение, активное обучение продолжает собирать информацию и рекомендуем предложения для вашей базы знаний. 

## <a name="best-practices"></a>Рекомендации

Рекомендации по использованию активного обучения см. в статье [Рекомендации по использованию базы знаний QnA Maker](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Дальнейшие действия
 
> [!div class="nextstepaction"]
> [Использование метаданных с GenerateAnswer API](metadata-generateanswer-usage.md)
