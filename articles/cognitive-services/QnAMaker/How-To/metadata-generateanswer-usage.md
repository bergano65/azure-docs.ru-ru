---
title: Метаданные с использованием API GenerateAnswer — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет добавлять метаданные в виде пар "ключ-значение" в наборы вопросов и ответов. Можно фильтровать результаты для пользовательских запросов и хранения дополнительной информации, которая может использоваться в дальнейших обсуждениях.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 99c076d7f26638833b568935e766cf319d21945e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443469"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Получение ответа с помощью GenerateAnswer API и метаданные

Чтобы получить прогнозируемый ответ на вопрос пользователя, используйте GenerateAnswer API. При публикации базы знаний, можно просмотреть информацию о том, как использовать этот API в **публикации** страницы. Можно также настройка API для фильтрации ответы, основанные на теги метаданных и проверка базы знаний из конечной точки с параметром строки запроса теста.

QnA Maker позволяет добавлять метаданные в виде пар ключ-значение в наборах вопросов и ответов. Эти сведения затем можно использовать для фильтрации результатов на запросы пользователей и для хранения дополнительной информации, которая может использоваться в дальнейших обсуждениях. Дополнительные сведения см. в разделе [Knowledge base](../Concepts/knowledge-base.md) (База знаний).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Store вопросов и ответов с сущностью QnA

Важно понимать, каким образом QnA Maker сохраняет данные вопросов и ответов. На рисунке ниже показана сущность QnA.

![Иллюстрация QnA сущности](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

У каждой сущности QnA имеется уникальный постоянный идентификатор. Идентификатор можно использовать для обновления для конкретной сущности QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Получение ответов прогнозы с помощью GenerateAnswer API

Использовании [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) в программ-роботов или приложению запрашивать знаний с вопросом пользователю, чтобы получить наиболее подходящий из вопросов и ответов задает.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Публикация в конечную точку GenerateAnswer получить 

После публикации базы знаний, либо из [портал QnA Maker](https://www.qnamaker.ai), или с помощью [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), можно получить сведения о конечной точке GenerateAnswer.

Вот как это можно сделать.
1. Войдите на портал [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. В **моей базы знаний**выберите **Просмотр кода** для вашей базы знаний.
    ![Снимок экрана My базы знаний](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Получите сведения о конечной точке GenerateAnswer.

    ![Снимок экрана: сведения о конечной точке](../media/qnamaker-how-to-metadata-usage/view-code.png)

Можно также получить сведения о конечной точки на вкладке **Settings** (Параметры) базы знаний.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Настройка запроса GenerateAnswer

Для вызова GenerateAnswer используется HTTP-запрос POST. Пример кода, в котором демонстрируется вызов GenerateAnswer, доступен в этих [кратких руководствах](../quickstarts/csharp.md). 

Использует запрос POST:

* Требуется [параметры URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Требуется [свойство header](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, для обеспечения безопасности
* Требуется [текст свойства](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

GenerateAnswer URL-адрес имеет следующий формат: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Не забудьте установить свойство заголовка HTTP `Authorization` со значением строки `EndpointKey ` завершающими пространства затем ключ конечной точки, найденный в **параметры** страницы.

Пример текста JSON выглядит как:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Свойства ответа GenerateAnswer

[Ответа](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful_query) представляет собой объект JSON, включая все сведения, необходимые для отображения ответа, а также следующие включить в диалоге, если он доступен.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Используйте QnA Maker с bot вC#

Bot framework предоставляет доступ к свойствам QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Поддержка bot имеет [пример](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) следующим кодом.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Используйте QnA Maker с ботом в Node.js

Bot framework предоставляет доступ к свойствам QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Поддержка bot имеет [пример](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) следующим кодом.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Фильтрация ответов по тегам пользовательских метаданных с помощью метаданных

Добавление метаданных позволяет фильтровать ответы, эти теги метаданных. Добавить столбец метаданных из **параметры представления** меню. Добавить метаданные в базе знаний, выбрав метаданные **+** значок, чтобы добавить пару метаданных. Эта пара состоит из одного ключа и одно значение.

![Снимок экрана: Добавление метаданных](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Фильтрация результатов с помощью strictFilters по тегам метаданных

Рассмотрим вопрос пользователя «При ли закрыть этот отеле?», где цель подразумевается для ресторан «Paradise.»

Так как результаты вполне достаточно ресторан «Paradise», можно задать фильтр в вызове GenerateAnswer на метаданные «Ресторан Name». Это показано в следующем примере:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Используйте результаты вопросов и ответов, чтобы сохранить контекст диалога

Ответ GenerateAnswer содержит соответствующие сведения о метаданных из набор вопросов и ответов. Эти сведения можно использовать в клиентском приложении для постоянного сохранения контекста предыдущих диалога для использования в диалогах более поздней версии. 

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

## <a name="match-questions-only-by-text"></a>Соответствует только, вопросы по тексту

По умолчанию QnA Maker выполняет поиск по вопросов и ответов. Если вы хотите просмотреть только вопросы, чтобы создать ответ, используйте `RankerType=QuestionOnly` в тело запроса POST GenerateAnswer запроса.

Можно выполнять поиск по опубликованной базы знаний, с помощью `isTest=false`, или в КБ тестирования с помощью `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Дальнейшие действия

**Публикации** страница также содержит сведения для создания ответа с помощью [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) и [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Создание базы знаний](./create-knowledge-base.md)
