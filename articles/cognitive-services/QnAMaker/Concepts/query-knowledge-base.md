---
title: Запрос базы знаний - NA Maker
description: Должна быть опубликована база знаний. После публикации база знаний запрашивается в точке прогнозирования времени выполнения с помощью API-известь.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220724"
---
# <a name="query-the-knowledge-base-for-answers"></a>Запрос базы знаний для ответов

Должна быть опубликована база знаний. После публикации база знаний запрашивается в точке прогнозирования времени выполнения с помощью API-известь. Запрос включает текст вопроса и другие настройки, чтобы помочь создателю nA выбрать наилучший возможный вариант ответа.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Как nA Maker обрабатывает пользовательский запрос, чтобы выбрать лучший ответ

Обученная и [опубликованная](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) база знаний NA Maker получает запрос пользователя от бота или другого клиентского приложения на [API GenerateAnswer.](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage) Следующая диаграмма иллюстрирует процесс получения запроса пользователя.

![Процесс ранжирования модели для запроса пользователя](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Процесс ранкера

Этот процесс объясняется в следующей таблице.

|Шаг|Назначение|
|--|--|
|1|Клиентское приложение отправляет запрос пользователя на [API GenerateAnswer.](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)|
|2|Создатель nA предварительно обрабатывает пользовательский запрос с помощью обнаружения языка, орфографических и выключателей слов.|
|3|Эта предварительная обработка используется для изменения запроса пользователя для достижения наилучших результатов поиска.|
|4|Этот измененный запрос отправляется в Azure Cognitive Search `top` Index, который получает количество результатов. Если правильный ответ не в этих результатах, `top` увеличьте значение немного. Как правило, значение 10 для `top` работ в 90% запросов.|
|5|Для определения сходства между пользовательским запросом и полученными результатами компания использует синтаксическую и семантическую феатурацию на основе семантической основе.|
|6|Модель машино-изученного ranker использует различные характеристики, от шага 5, для того чтобы обусловить счеты доверия и новый заказ ранжирования.|
|7|Новые результаты возвращаются в клиентское приложение в ранжированном порядке.|
|||

Функции, используемые включают, но не ограничиваются семантикой уровня слова, значением терминного уровня в корпусе и глубоко выученными семантическими моделями для определения сходства и актуальности между двумя текстовыми строками.

## <a name="http-request-and-response-with-endpoint"></a>ЗАПРОС и ответ HTTP с конечными точками
Когда вы публикуете свою базу знаний, служба создает на основе REST http конечную точку, которая может быть интегрирована в ваше приложение, как правило, чат-бот.

### <a name="the-user-query-request-to-generate-an-answer"></a>Запрос запроса пользователя для создания ответа

Запрос пользователя — это вопрос, который конечный пользователь задает базе знаний, `How do I add a collaborator to my app?`например. Запрос часто находится в естественном языке формата или несколько `help with collaborators`ключевых слов, которые представляют вопрос, например. Запрос отправляется в вашу базу знаний из запроса HTTP в клиентском приложении.

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

Вы контролируете ответ, устанавливая свойства, такие как [scoreThreshold,](./confidence-score.md#choose-a-score-threshold) [top,](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)и [strictFilters.](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)

Используйте [контекст разговора](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) с [многоступенчатой функциональностью,](../how-to/multiturn-conversation.md) чтобы сохранить разговор будет уточнить вопросы и ответы, чтобы найти правильный и окончательный ответ.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Ответ от призыва к генерации ответа

Ответ HTTP — это ответ, полученный из базы знаний, основанный на наилучшем соответствии для данного запроса пользователя. Ответ включает в себя ответ и счет предсказания. Если вы попросили более одного `top` главного ответа с собственностью, вы получите более одного верхнего ответа, каждый со счетом.

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
> [Оценка доверия](./confidence-score.md)
