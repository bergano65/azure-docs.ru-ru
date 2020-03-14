---
title: Запрос базы знаний — QnA Maker
description: База знаний должна быть опубликована. После публикации база знаний запрашивается на конечной точке прогнозирования времени выполнения с помощью API Женератеансвер.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220724"
---
# <a name="query-the-knowledge-base-for-answers"></a>Запрос ответов в базе знаний

База знаний должна быть опубликована. После публикации база знаний запрашивается на конечной точке прогнозирования времени выполнения с помощью API Женератеансвер. Запрос содержит текст вопроса и другие параметры, помогающие QnA Maker выбора наилучшего совпадения с ответом.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Как QnA Maker обрабатывает запрос пользователя для выбора лучшего ответа

База знаний с обученным и [опубликованным](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker получает пользовательский запрос от робота или другого клиентского приложения на [API женератеансвер](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). На следующей схеме показан процесс получения запроса пользователя.

![Процесс ранжирования модели для пользовательского запроса](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Процесс ранжирования

Этот процесс описан в следующей таблице.

|Шаг|Назначение|
|--|--|
|1|Клиентское приложение отправляет пользовательский запрос в [API женератеансвер](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker предварительно обрабатывает пользовательский запрос с помощью распознавания языка, средств проверки орфографии и средств разбиения по словам.|
|3|Эта предварительная обработка используется для изменения пользовательского запроса для получения лучших результатов поиска.|
|4|Этот измененный запрос отправляется в индекс Azure Когнитивный поиск, который получает `top` число результатов. Если нужный ответ не указан в этих результатах, увеличьте значение `top` немного. Как правило, значение 10 для `top` работает в 90% запросов.|
|5|QnA Maker использует синтаксические и семантические Добавление признаков для определения сходства между пользовательским запросом и полученными результатами QnA.|
|6|В модели ранжирования, реализованной на компьютере, используются различные функции из шага 5, чтобы определить рейтинг достоверности и новый порядок ранжирования.|
|7|Новые результаты возвращаются клиентскому приложению в порядке ранжирования.|
|||

Используемые функции включают в себя, но не ограниченную семантику на уровне слов, важность на уровне терминов в совокупности и семантические изученные модели для определения сходства и релевантности между двумя текстовыми строками.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-запрос и ответ с конечной точкой
При публикации базы знаний служба создает конечную точку HTTP на основе RESTFUL, которая может быть интегрирована в приложение, как правило, с помощью программы-робота Chat.

### <a name="the-user-query-request-to-generate-an-answer"></a>Запрос пользователя на создание ответа

Пользовательский запрос — это вопрос, который пользователь запрашивает в базе знаний, например `How do I add a collaborator to my app?`. Запрос часто выполняется в естественном языке или несколько ключевых слов, представляющих вопрос, например `help with collaborators`. Запрос отправляется в базу знаний из HTTP-запроса в клиентском приложении.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Вы управляете ответом, задавая такие свойства, как [скоресрешолд](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)и [стриктфилтерс](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Используйте [контекст диалога](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) с [функцией множественной переворачивания](../how-to/multiturn-conversation.md) , чтобы диалоговое обсуждение продолжало находить вопросы и ответы, чтобы найти правильный и окончательный ответ.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Ответ от вызова для создания ответа

Ответ HTTP — это ответ, полученный из базы знаний, в зависимости от наилучшего соответствия для данного пользовательского запроса. Ответ включает ответ и оценку прогноза. Если вы запросили более одного первого ответа со свойством `top`, вы получаете более одного первого ответа, каждый из которых имеет оценку.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Оценка достоверности](./confidence-score.md)
