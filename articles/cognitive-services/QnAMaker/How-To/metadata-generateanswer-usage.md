---
title: Метаданные с использованием API GenerateAnswer — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет добавлять метаданные в виде пар "ключ-значение" в наборы вопросов и ответов. Вы можете фильтровать результаты по запросам пользователей и хранить дополнительные сведения, которые можно использовать в дальнейших беседах.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/12/2019
ms.author: diberry
ms.openlocfilehash: 8f00ffeff4eb353fa70aa7df60b14c97d4b8e724
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554875"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Получение ответа с помощью API и метаданных Женератеансвер

Чтобы получить прогнозируемый ответ на вопрос пользователя, используйте API Женератеансвер. При публикации базы знаний можно просмотреть сведения об использовании этого API на странице **Публикация** . Вы также можете настроить API для фильтрации ответов на основе тегов метаданных и проверить базу знаний из конечной точки с помощью параметра строки тестового запроса.

QnA Maker позволяет добавлять метаданные в виде пар "ключ — значение" в наборы вопросов и ответов. Затем эти сведения можно использовать для фильтрации результатов в пользовательских запросах, а также для хранения дополнительных сведений, которые можно использовать в дальнейших беседах. Дополнительные сведения см. в разделе [Knowledge base](../Concepts/knowledge-base.md) (База знаний).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Хранение вопросов и ответов с помощью сущности QnA

Важно понимать, как QnA Maker хранит данные вопроса и ответов. На рисунке ниже показана сущность QnA.

![Иллюстрация сущности QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

У каждой сущности QnA имеется уникальный постоянный идентификатор. Идентификатор можно использовать для внесения обновлений в определенную сущность QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Получение прогнозов ответов с помощью API Женератеансвер

Используйте [API женератеансвер](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) в роботе или приложении для запроса базы знаний с пользовательским вопросом, чтобы получить наилучшее соответствие из набора вопросов и ответов.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Публикация для получения конечной точки Женератеансвер 

После публикации базы знаний либо с [портала QnA Maker](https://www.qnamaker.ai), либо с помощью [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)можно получить сведения о конечной точке женератеансвер.

Вот как это можно сделать.
1. Войдите на портал [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. В окне **Мои базы знаний**выберите **Просмотреть код** для своей базы знаний.
    ![Screenshot баз знаний ](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Получите сведения о конечной точке GenerateAnswer.

    ![Снимок экрана сведений о конечной точке](../media/qnamaker-how-to-metadata-usage/view-code.png)

Можно также получить сведения о конечной точки на вкладке **Settings** (Параметры) базы знаний.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Конфигурация запроса Женератеансвер

Для вызова GenerateAnswer используется HTTP-запрос POST. Пример кода, в котором демонстрируется вызов GenerateAnswer, доступен в этих [кратких руководствах](../quickstarts/create-publish-kb-csharp-sdk.md#generate-an-answer-from-the-knowledge-base). 

Запрос POST использует:

* Обязательные [Параметры URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Требуемое [свойство заголовка](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, для безопасности
* Обязательные [Свойства текста](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

URL-адрес Женератеансвер имеет следующий формат: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Не забудьте задать свойство "заголовок HTTP" `Authorization` со значением строки, `EndpointKey` с конечным пробелом, после чего ключ конечной точки находится на странице **параметров** .

Пример текста JSON выглядит следующим образом:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Дополнительные сведения о [ранкертипе](../concepts/best-practices.md#choosing-ranker-type).

Предыдущий JSON запросил только ответы, которые имеют пороговую оценку 30% или выше. 

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Свойства ответа Женератеансвер

[Ответ](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) — это объект JSON, включающий всю информацию, необходимую для вывода ответа, и следующий ход в диалоге, если он доступен.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Предыдущий формат JSON ответил на ответ с показателем 38,5%. 

## <a name="use-qna-maker-with-a-bot-in-c"></a>Использование QnA Maker с Bot вC#

Платформа Bot предоставляет доступ к свойствам QnA Maker с помощью API- [интерфейса «ответ](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)»:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Предыдущий JSON запросил только ответы, которые имеют пороговую оценку 30% или выше. 

В коде программы-робота поддержки есть [Пример](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) с этим кодом.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Использование QnA Maker с программой-роботом в Node. js

Платформа Bot предоставляет доступ к свойствам QnA Maker с помощью API- [интерфейса «ответ](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)»:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Предыдущий JSON запросил только ответы, которые имеют пороговую оценку 30% или выше. 

В коде программы-робота поддержки есть [Пример](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) с этим кодом.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Использование метаданных для фильтрации ответов по тегам пользовательских метаданных

Добавление метаданных позволяет фильтровать ответы по этим тегам метаданных. Добавьте столбец метаданных из меню **Параметры представления** . Добавьте метаданные в базу знаний, щелкнув значок метаданных **+** , чтобы добавить пару метаданных. Эта пара состоит из одного ключа и одного значения.

![Снимок экрана: Добавление метаданных](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Фильтрация результатов с помощью strictFilters по тегам метаданных

Примите во внимание вопрос пользователя "когда закроется это отель?", где предполагается, что цель предполагается для ресторана "сообразительного".

Поскольку результаты требуются только для ресторана "сообразительного", можно установить фильтр в вызове Женератеансвер для метаданных "имя ресторана". Это показано в следующем примере:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Использование результатов вопросов и ответов для сохранения контекста беседы

Ответ на Женератеансвер содержит соответствующие метаданные соответствующего набора вопросов и ответов. Эти сведения можно использовать в клиентском приложении для хранения контекста предыдущего диалога, который будет использоваться в последующих диалогах. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Сопоставлять только вопросы по тексту

По умолчанию QnA Maker выполняет поиск по вопросам и ответам. Если вы хотите выполнять поиск только по вопросам, то для получения ответа используйте `RankerType=QuestionOnly` в тексте сообщения запроса Женератеансвер.

Вы можете выполнять поиск в опубликованной базе знаний, используя `isTest=false` или в тесте базы знаний с помощью `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Распространенные ошибки HTTP

|Код|Пояснение|
|:--|--|
|"2xx"|Успешно|
|400|Параметры запроса указаны неправильно. Это означает, что требуемые параметры отсутствуют, имеют неправильный формат или слишком большой размер|
|400|Текст запроса указан неправильно. Это означает, что JSON отсутствует, имеет неправильный формат или слишком большой размер|
|401|Недопустимый ключ|
|403|Доступ запрещен. У вас нет необходимых разрешений|
|404|База знаний не существует|
|410|Этот API устарел и больше недоступен|

## <a name="next-steps"></a>Дальнейшие действия

На странице **Публикация** также содержатся сведения для создания ответа с помощью [POST](../Quickstarts/get-answer-from-kb-using-postman.md) и [перелистывания](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Создание программы-робота в базе знаний](../tutorials/integrate-qnamaker-luis.md)
