---
title: Аналитика в базе знаний — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker сохраняет все журналы чатов и другие данные телеметрии, если во время создания службы QnA Maker вы включили App Insights. Чтобы получить журналы чатов из App Insights, воспользуйтесь приведенными здесь примерами запросов.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 72d2598c1ff17f80fc264e6d547a799ab74a163f
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621979"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Получение аналитических данных о базе знаний

QnA Maker сохраняет все журналы чатов и другие данные телеметрии, если во время [создания службы QnA Maker](./set-up-qnamaker-service-azure.md) вы включили App Insights. Чтобы получить журналы чатов из App Insights, воспользуйтесь приведенными здесь примерами запросов.

1. Перейдите к нужному ресурсу App Insights.

    ![Выбор ресурса Application Insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Выберите **Log (Analytics)** . Откроется новое окно, в котором можно запросить данные телеметрии QnA Maker.

3. Вставьте следующий запрос и выполните его.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Щелкните **Запуск**, чтобы выполнить запрос.

    [![выполнения запроса для определения вопросов, ответов и оценок пользователей](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Выполнение запросов для получения других аналитических данных о базе знаний QnA Maker

### <a name="total-90-day-traffic"></a>Общий трафик за 90 дней

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer" 
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Общий трафик вопросов за определенный период времени

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST" 
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer" 
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Трафик пользователя

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId 
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Распределение задержки вопросов

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Вопросы без ответа

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0"
| project timestamp, KbId, question, answer, score 
| order  by timestamp  desc
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Выберите капактий](../tutorials/choosing-capacity-qnamaker-deployment.md)
