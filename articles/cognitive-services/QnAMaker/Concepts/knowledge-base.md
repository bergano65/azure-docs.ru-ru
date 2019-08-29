---
title: Базы знаний — QnA Maker
titleSuffix: Azure Cognitive Services
description: База знаний QnA Maker состоит из наборов пар вопросов и ответов (QnA) и дополнительных метаданных, связанных с каждой парой QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b3e74f337cf8f57321c3a8d94f8191fc3ebb530
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093900"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Что представляет собой база знаний QnA Maker?

База знаний QnA Maker состоит из наборов пар вопросов и ответов (QnA) и дополнительных метаданных, связанных с каждой парой QnA.

## <a name="key-knowledge-base-concepts"></a>Ключевые понятия базы знаний

* **Вопросы**. Вопрос содержит текст, который наилучшим образом представляет пользовательский запрос. 
* **Ответы**. Ответ, который возвращается, когда пользовательский запрос сопоставляется с соответствующим вопросом.  
* **Метаданные**. Метаданные — это теги, которые связаны с парой QnA и отображаются в виде пар "ключ — значение". Теги метаданных используются для фильтрации пар QnA и ограничения набора, в котором выполняется сопоставление запросов.

Единственный QnA, представленный числовым идентификатором QnA, имеет несколько вариантов вопроса (альтернативные вопросы), которые сопоставляются с одним ответом. Кроме того, каждая такая пара может иметь несколько связанных с ними полей метаданных: один ключ и одно значение.

![Базы знаний QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Формат содержимого базы знаний

При приеме содержимого в базу знаний QnA Maker пытается преобразовать контент в markdown. Подробнее о форматах markdown, понятных большинству чат-клиентов, см. в [этом](https://aka.ms/qnamaker-docs-markdown-support) блоге.

Поля метаданных состоят из пар "ключ — значение", разделенных двоеточием **(Продукт:Уничтожитель)** . Ключ и значение должны быть только текстом. Ключ метаданных не должен содержать пробелов. Метаданные поддерживают только одно значение для каждого ключа.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Как QnA Maker обрабатывает запрос пользователя для выбора лучшего ответа

База знаний с обученным и [опубликованным](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker получает пользовательский запрос от робота или другого клиентского приложения на [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). На следующей схеме показан процесс получения запроса пользователя.

![Процесс ранжирования для пользовательского запроса](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Процесс описан в следующей таблице.

|Шаг|Цель|
|--|--|
|1|Клиентское приложение отправляет пользовательский запрос в [API женератеансвер](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker предварительно обрабатывает пользовательский запрос с помощью распознавания языка, средств проверки орфографии и средств разбиения по словам.|
|3|Эта предварительная обработка предпринимается для изменения пользовательского запроса для получения лучших результатов поиска.|
|4|Этот измененный запрос отправляется в индекс службы поиска Azure, `top` получая количество результатов. Если нужный ответ не указан в этих результатах, увеличьте значение `top` немного. Как правило, значение 10 для `top` Works в 90% запросов.|
|5|QnA Maker применяет расширенные Добавление признаков, чтобы определить правильность полученного запроса результатов поиска Azure для пользователя. |
|6|В обученной модели ранжирования для ранжирования результатов поиска Azure используется оценка функции, полученная на шаге 5.|
|7|Новые результаты возвращаются клиентскому приложению в порядке ранжирования.|
|||

Используемые функции включают в себя не только семантику на уровне Word, важность на уровне терминов в совокупности, так и семантические изученные семантики для определения сходства и релевантности между двумя текстовыми строками.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-запрос и ответ с конечной точкой
При публикации базы знаний служба создает **конечную точку** HTTP на основе RESTful, которая может быть интегрирована в приложение, как правило, с помощью программы-робота Chat. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Запрос пользователя на создание ответа

**Пользовательский запрос** — это вопрос, который пользователь запрашивает в базе знаний, например, `How do I add a collaborator to my app?`. Запрос часто выполняется в естественном языке или несколько ключевых слов, представляющих вопрос, например, `help with collaborators`. Запрос отправляется на ваш набор знаний из HTTP- **запроса** в клиентском приложении.

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

Вы управляете ответом, задавая такие свойства, как [скоресрешолд](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)и [стриктфилтерс](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Используйте [контекст диалога](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) с [функцией множественной переворачивания](../how-to/multiturn-conversation.md) , чтобы диалоговое обсуждение продолжало находить вопросы и ответы, чтобы найти правильный и окончательный ответ.

### <a name="the-response-from-a-call-to-generate-answer"></a>Ответ от вызова для создания ответа

**Ответ** HTTP — это ответ, полученный из базы знаний, в зависимости от наилучшего соответствия для данного пользовательского запроса. Ответ включает ответ и оценку прогноза. Если вы запросили более одного первого ответа, со `top` свойством вы получаете более одного первого ответа, каждый из которых имеет оценку. 

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

### <a name="test-and-production-knowledge-base"></a>База знаний тестирования и рабочей базы
База знаний является репозиторием вопросов и ответов, который создан, обслуживается и используется QnA Maker. Каждый уровень QnA Maker может использоваться для нескольких баз знаний.

База знаний имеет два состояния: "Тестирование" и "Опубликованная". 

**База знаний тестирования** — это версия, которая редактируется, сохраняется и проверяется на точность и полноту ответов. Изменения, внесенные в тестовую базу знаний, не влияют на конечного пользователя приложения или чат бота. База знаний теста называется `test` в HTTP-запросе. 

**Опубликованная база знаний** — это версия, которая используется в роботе или приложении чата. Действие публикации перемещает содержимое базы знаний в состоянии "Тестирование" в базу знаний в состоянии "Опубликованная". Поскольку база знаний в состоянии "Опубликованная" является версией, которую использует приложение через конечную точку, следует убедиться в наличии правильного и хорошо проверенного содержимого. Опубликованная база знаний известна как `prod` в HTTP-запросе. 

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Жизненный цикл базы знаний](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>См. также

[Общие сведения о QnA Maker](../Overview/overview.md)

Создать и изменить базу знаний с помощью: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Пакет SDK для .NET](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Создать ответ с: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Пакет SDK для .NET](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
