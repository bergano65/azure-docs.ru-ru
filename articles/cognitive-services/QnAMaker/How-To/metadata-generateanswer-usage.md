---
title: Метаданные с использованием API GenerateAnswer — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет добавлять метаданные в виде пар "ключ-значение" в наборы вопросов и ответов. Эти сведения можно использовать, чтобы отфильтровать результаты запросов пользователей и сохранить дополнительную информацию, которая может использоваться в дальнейших диалогах.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.openlocfilehash: 2454e07e4fc4600f846acc7afbcc19cc0b677450
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792238"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Получение ответа базы знаний с помощью GenerateAnswer API и метаданные

Чтобы получить прогнозируемый ответ на вопрос пользователя, используйте GenerateAnswer API. При публикации базы знаний, эту информацию, чтобы использовать этот API отображается на странице публикации. Можно также интерфейса API, чтобы отфильтровать ответы, основанные на теги метаданных и протестировать базы знаний из конечной точки с параметром строки запроса теста.

QnA Maker позволяет добавлять метаданные в виде пар "ключ — значение" в наборы вопросов и ответов. Эти сведения можно использовать, чтобы отфильтровать результаты запросов пользователей и сохранить дополнительную информацию, которая может использоваться в дальнейших диалогах. Дополнительные сведения см. в разделе [Knowledge base](../Concepts/knowledge-base.md) (База знаний).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Хранение вопросов и ответов с сущностью QnA

Сначала важно понять, как QnA Maker хранит данные вопросов и ответов. На рисунке ниже показана сущность QnA.

![Сущность QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

У каждой сущности QnA имеется уникальный постоянный идентификатор. Этот идентификатор можно использовать для внесения изменений в определенную сущность QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Получение ответов прогнозы с помощью GenerateAnswer API

API GenerateAnswer можно использовать в боте или приложении для отправки к базе знаний запросов с вопросом пользователя для получения наиболее точного ответа из наборов вопросов и ответов.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Публикация в конечную точку GenerateAnswer получить 

После публикации базы знаний с помощью [портала QnA Maker](https://www.qnamaker.ai) или [API](https://go.microsoft.com/fwlink/?linkid=2092179) можно получить сведения о конечной точке GenerateAnswer.

Вот как это можно сделать.
1. Войдите на портал [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. В разделе **My knowledge bases** (Мои базы знаний) щелкните **View Code** (Просмотреть код) для своей базы знаний.
    Раздел ![My knowledge bases](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png) (Мои базы знаний)
1. Получите сведения о конечной точке GenerateAnswer.

    ![Сведения о конечной точке](../media/qnamaker-how-to-metadata-usage/view-code.png)

Можно также получить сведения о конечной точки на вкладке **Settings** (Параметры) базы знаний.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Настройка запроса GenerateAnswer

Для вызова GenerateAnswer используется HTTP-запрос POST. Пример кода, в котором демонстрируется вызов GenerateAnswer, доступен в этих [кратких руководствах](../quickstarts/csharp.md).

**URL-адрес запроса** имеет следующий формат: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Свойство запроса HTTP|ИМЯ|type|Назначение|
|--|--|--|--|
|Параметр URL-адреса маршрута|Идентификатор базы знаний|string|Идентификатор GUID для базы знаний.|
|Параметр URL-адреса маршрута|Узел конечной точки QnAMaker|string|Имя узла конечной точки, развернутой в вашей подписке Azure. Доступно на странице "Параметры" после ее публикации в базе знаний. |
|Верхний колонтитул|Content-Type|string|Тип носителя текста, отправляемого в API. Значение по умолчанию —: ''|
|Верхний колонтитул|Авторизация|string|Ключ конечной точки (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Текст запроса POST|Объект JSON|JSON|Вопрос с параметрами|


Текст JSON имеет несколько параметров:

|Свойство текста JSON|Обязательно для заполнения|type|Назначение|
|--|--|--|--|
|`question`|Требуется|string|Вопрос пользователя отправки на базе знаний.|
|`top`|дополнительный|integer|Количество ранжированных результатов в выходных данных. Значение по умолчанию — 1.|
|`userId`|дополнительный|string|Уникальный идентификатор для определения пользователя. Этот идентификатор будет записываться в журналы чата.|
|`scoreThreshold`|дополнительный|integer|Будут возвращены только ответы с оценкой степени достоверности превышает данное пороговое значение. Значение по умолчанию — 0.|
|`isTest`|дополнительный|Логическое|Если установлено значение true, возвращает результаты из `testkb` индекса поиска вместо опубликованного индекса.|
|`strictFilters`|дополнительный|string|Если указан, QnA Maker будет возвращать только ответы с указанными метаданными. Используйте `none` для указания, ответ должен иметь ни одного фильтра метаданных. |

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

Успешный ответ возвращается в состояние 200 и ответ JSON. 

|Свойство ответы (Сортировка по оценке)|Назначение|
|--|--|
|приложения|Оценка от 0 до 100.|
|Идентификатор|Уникальный идентификатор, назначенный ответу.|
|Вопросы|Вопросы пользователя.|
|Ответ|Ответ на вопрос.|
|источник|Имя источника ответа, который был извлечен или сохранен в базе знаний.|
|метаданные|Метаданные, связанные с ответом.|
|Metadata.Name|Имя метаданных (строка, максимальная длина 100, обязательный параметр.|
|Metadata.Value: Значение метаданных. (строка, максимальная длина 100, обязательный параметр.|


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

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Использование метаданных позволяет фильтровать ответы по настраиваемые теги метаданных

Добавление метаданных позволяет фильтровать ответы, эти теги метаданных. Добавить столбец метаданных из **параметры представления** меню. Добавить метаданные в базе знаний, щелкнув метаданные **+** значок, чтобы добавить пару метаданных. Эта пара состоит из одного ключа и одно значение.

![Добавление метаданных](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Фильтрация результатов с помощью strictFilters по тегам метаданных

Рассмотрим вопрос пользователя "Когда закрывается этот отель?", когда подразумевается ресторан "Paradise".

Так как требуются результаты только для ресторана "Paradise", в вызове GenerateAnswer можно задать фильтр по метаданным "Restaurant Name" (Название ресторана), как показано ниже.

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

< имя = «keep-context» ></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Используйте результаты вопросов и ответов, чтобы сохранить контекст диалога

Ответ GenerateAnswer содержит соответствующие сведения о метаданных набора соответствующих ответов. Эти сведения можно использовать в клиентском приложении для постоянного сохранения контекста предыдущих диалога для использования в диалогах более поздней версии. 

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

## <a name="next-steps"></a>Дальнейшие действия

На странице публикации также предоставляются сведения для создания ответа с помощью [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) и [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Создание базы знаний](./create-knowledge-base.md)
