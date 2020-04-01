---
title: Метаданные с использованием API GenerateAnswer — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет добавлять метаданные в виде пар "ключ-значение" в наборы вопросов и ответов. Вы можете отфильтровывать результаты на запросы пользователей и хранить дополнительную информацию, которая может быть использована в последующих беседах.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: diberry
ms.openlocfilehash: 9beb6dbbba1c5855b8bfa97fc02f50aa59225d78
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474858"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Получить ответ с помощью API Иана и метаданных GenerateAnswer

Чтобы получить прогнозируемый ответ на вопрос пользователя, используйте API GenerateAnswer. Когда вы публикуете базу знаний, вы можете увидеть информацию о том, как использовать этот API на странице **Публикации.** Можно также настроить API для фильтрации ответов на основе тегов метаданных и проверить базу знаний с точки зрения конечных данных с параметром строки строки запроса тестирования.

В наборы вопросов и ответов позволяет добавлять метаданные в виде ключевых и ценовых пар. Затем эту информацию можно использовать для фильтрации результатов в запросах пользователей и для хранения дополнительной информации, которая может быть использована в последующих беседах. Дополнительные сведения см. в разделе [Knowledge base](../Concepts/knowledge-base.md) (База знаний).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Храните вопросы и ответы с помощью сущности ЗНА

Важно понять, как компания «NA Maker» хранит данные о вопросах и отвечает на них. На рисунке ниже показана сущность QnA.

![Иллюстрация сущности ЗНА](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

У каждой сущности QnA имеется уникальный постоянный идентификатор. Идентификатор можно использовать для обновления определенной сущности nA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Получайте прогнозы ответов с помощью API GenerateAnswer

Вы используете [API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) в вашем боте или приложении для запроса базы знаний с пользовательским вопросом, чтобы получить лучшее соответствие из набора вопросов и ответов.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Публикация, чтобы получить конечную точку GenerateAnswer

После публикации базы знаний, либо с [портала NA Maker,](https://www.qnamaker.ai)либо с помощью [API,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)вы можете получить подробную информацию о вашей конечной точке GenerateAnswer.

Вот как это можно сделать.
1. Выполните вход на странице [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. В **моих базах знаний**, выберите **View Code** для вашей базы знаний.
    ![Скриншот моих баз знаний](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Получите сведения о конечной точке GenerateAnswer.

    ![Скриншот деталей конечной точки](../media/qnamaker-how-to-metadata-usage/view-code.png)

Можно также получить сведения о конечной точки на вкладке **Settings** (Параметры) базы знаний.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Конфигурация запроса GenerateAnswer

Для вызова GenerateAnswer используется HTTP-запрос POST. Пример кода, в котором демонстрируется вызов GenerateAnswer, доступен в этих [кратких руководствах](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

Запрос POST использует:

* [Требуемые параметры URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Требуемое свойство заголовка, `Authorization`для обеспечения безопасности
* Необходимые [свойства тела.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)

URL GenerateAnswer имеет следующий формат:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Не забудьте установить свойство заголовка `Authorization` HTTP `EndpointKey` со значением строки с задним пространством, а затем ключом конечной точки, найденным на странице **«Настройки».**

Пример тела JSON выглядит следующим:

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

Узнайте больше о [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Предыдущий JSON запрашивал только ответы, которые находятся на 30% или выше порогового балла.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Свойства ответа на генерацию ответов Ответов

[Ответ](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) — это объект JSON, включающее всю информацию, необходимую для отображения ответа и следующего поворота в разговоре, если она доступна.

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

Предыдущий JSON ответил ответом с результатом 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Используйте nA Maker с ботом в C #

Платформа бота обеспечивает доступ к свойствам создателя nA с [помощью API getAnswer:](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)

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

Предыдущий JSON запрашивал только ответы, которые находятся на 30% или выше порогового балла.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Используйте nA Maker с ботом в Node.js

Платформа бота обеспечивает доступ к свойствам создателя nA с [помощью API getAnswer:](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)

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

Предыдущий JSON запрашивал только ответы, которые находятся на 30% или выше порогового балла.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Используйте метаданные для фильтрации ответов пользовательскими тегами метаданных

Добавление метаданных позволяет фильтровать ответы с помощью этих тегов метаданных. Добавьте столбец метаданных из меню **«Параметры представления».** Добавьте метаданные в базу знаний, **+** выбрав значок метаданных для добавления пары метаданных. Эта пара состоит из одного ключа и одного значения.

![Скриншот добавления метаданных](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Фильтрация результатов с помощью strictFilters по тегам метаданных

Рассмотрим вопрос пользователя "Когда этот отель закрывается?", где подразумевается намерение для ресторана "Рай".

Поскольку результаты необходимы только для ресторана "Рай", вы можете установить фильтр в generateAnswer вызова на метаданные "Название ресторана". Следующий пример показывает следующее:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Используйте результаты вопросов и ответов для поддержания контекста разговора

Ответ на GenerateAnswer содержит соответствующую информацию о метаданных по соответствующему набору вопросов и ответов. Эту информацию можно использовать в клиентском приложении для хранения контекста предыдущего разговора для использования в последующих беседах.

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

## <a name="match-questions-only-by-text"></a>Матч только вопросы, по тексту

По умолчанию, создатель nA выполняет поиск вопросов и ответов. Если вы хотите искать только вопросы, чтобы `RankerType=QuestionOnly` получить ответ, используйте в органе POST запрос GenerateAnswer.

Вы можете искать через опубликованные `isTest=false`кб, используя, `isTest=true`или в тесте кб с помощью .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Общие ошибки HTTP

|Код|Объяснение|
|:--|--|
|"2xx"|Успешно|
|400|Параметры запроса указаны неправильно. Это означает, что требуемые параметры отсутствуют, имеют неправильный формат или слишком большой размер|
|400|Текст запроса указан неправильно. Это означает, что JSON отсутствует, имеет неправильный формат или слишком большой размер|
|401|Недопустимый ключ|
|403|Доступ запрещен. У вас нет необходимых разрешений|
|404|База знаний не существует|
|410|Этот API устарел и больше недоступен|

## <a name="next-steps"></a>Следующие шаги

**Страница Publish** также предоставляет информацию для получения ответа с почтальоном или CURL. [generate an answer](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

> [!div class="nextstepaction"]
> [Создание чат-бота базы знаний](../tutorials/integrate-qnamaker-luis.md)
