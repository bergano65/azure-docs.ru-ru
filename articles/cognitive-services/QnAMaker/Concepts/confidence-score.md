---
title: Оценка достоверности — QnA Maker
titleSuffix: Azure Cognitive Services
description: Оценка достоверности означает степень достоверности того, что ответ соответствует этому запросу пользователя.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e2f7136ea7b973386eeb746a74ad09fadb490e83
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229108"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Оценка достоверности в базе знаний QnA Maker
Когда пользовательский запрос сопоставляется с базой знаний, API службы QnA Maker возвращает соответствующие ответы вместе с оценкой достоверности. Эта оценка означает степень достоверности того, что ответ соответствует этому запросу пользователя. 

Оценка достоверности находится в пределах от 0 до 100. Оценка 100, скорее всего, указывает на точное соответствие, а оценка 0 означает, что соответствия не обнаружено. Чем выше оценка — тем больше уверенность в ответе. Для запроса может вернуться несколько ответов. В этом случае ответы возвращаются в порядке уменьшения оценки достоверности.

В приведенном ниже примере представлена одна сущность QnA с 2 вопросами. 


![Пример пары QnA](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

В приведенном выше примере для разных типов пользовательских запросов можно ожидать такие оценки, как в диапазоне оценок ниже:


![Ранжированный диапазон оценок](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


В этой таблице приведено описание типичной достоверности и связанная с ней оценка.

|Значение оценки|Описание оценки|Пример запроса|
|--|--|--|
|90–100|Практически точное совпадение с запросом пользователя и вопросом о базе знаний|"После публикации мои изменения в базе знаний не обновляются"|
|> 70|Высокий уровень достоверности. Обычно это правильный ответ, который полностью отвечает на запрос пользователя|"Опубликованная база знаний не обновляется"|
|50–70|Средний уровень достоверности. Обычно довольно хороший ответ, соответствующий основному намерению в запросе пользователя.|"Следует ли сохранять обновления перед публикацией базы знаний?"|
|30–50|Низкий уровень достоверности. Обычно связанный ответ, который частично соответствует намерению в запросе пользователя.|"Что делает сохранение и обучение?"|
|< 30|Очень низкая достоверность. Обычно ответ на запрос пользователя не предоставляется, но выводятся некоторые совпадения слов или фраз. |"Где можно добавить синонимы в мою базу знаний"|
|0|Нет соответствия, поэтому ответ не возвращается.|"Сколько стоит использование службы"|

## <a name="choose-a-score-threshold"></a>Указание порогового значения оценки
В приведенной выше таблице показаны оценки, ожидаемые в большинстве баз знаний. However, since every KB is different, and has different types of words, intents, and goals- we recommend you test and choose the threshold that best works for you. By default the threshold is set to 0, so that all possible answers are returned. The recommended threshold that should work for most KBs, is **50**.

При выборе порогового значения не забывайте про баланс между точностью и охватом и настраивайте порог в соответствии со своими требованиями.

- Если **точность** важнее для вашего сценария, увеличьте пороговое значение. Таким образом, каждый возвращаемый ответ будет иметь гораздо более высокую степень достоверности и намного вероятнее будет соответствовать запросу пользователя. В этом случае многие другие вопросы могут остаться без ответа. *Например*, если пороговое значение — **70**, можно пропустить некоторые неоднозначные примеры, такие как "Что такое сохранение и обучение?".

- Если **охват** (или полнота) важнее, снизьте пороговое значение. Это поможет ответить на максимальное количество вопросов, даже при наличии только частичного отношения к вопросу пользователя. Это означает, что может быть больше случаев, когда ответ не соответствует фактическому запросу пользователя, а предоставляет лишь некоторые связанные сведения. *For example:* if you make the threshold **30**, you might give answers for queries like "Where can I edit my KB?"

> [!NOTE]
> В новых версиях QnA Maker улучшена логика оценки, что может повлиять на пороговые значения. Каждый раз при обновлении службы проверяйте и, при необходимости, изменяйте пороговые значения. Узнать текущую версию службы QnA вы можете [здесь](https://www.qnamaker.ai/UserSettings). Сведения о том, как получить последние обновления см. [здесь](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Set threshold 

Set the threshold score as a property of the [GenerateAnswer API JSON body](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). This means you set it for each call to GenerateAnswer. 

From the bot framework, set the score as part of the options object with [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) or [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Улучшение оценок достоверности
Чтобы улучшить оценку достоверности конкретного ответа на запрос пользователя, можно добавить запрос пользователя в базу знаний в качестве альтернативного вопроса для этого ответа. Вы можете также использовать [исправление слов](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) без учета регистра, чтобы добавить синонимы к ключевым словам в базе знаний.


## <a name="similar-confidence-scores"></a>Аналогичные оценки достоверности
Если несколько ответов имеют аналогичные оценки достоверности, вполне вероятно, что запрос был слишком общим и, следовательно, соответствовал с равной вероятностью с несколькими ответами. Попытайтесь структурировать QnAs лучше, чтобы каждый объект QnA имел четкое намерение.


## <a name="confidence-score-differences-between-test-and-production"></a>Confidence score differences between test and production
Оценка достоверности ответа может незначительно измениться между тестируемой и опубликованной версией базы знаний, даже если содержимое одинаковое. This is because the content of the test and the published knowledge base are located in different Azure Cognitive Search indexes. 

The test index holds all the QnA pairs of your knowledge bases. When querying the test index, the query applies to the entire index then results are restricted to the partition for that specific knowledge base. If the test query results are negatively impacting your ability to validate the knowledge base, you can:
* organize your knowledge base using one of the following:
    * 1 resource restricted to 1 KB: restrict your single QnA resource (and the resulting Azure Cognitive Search test index) to a single knowledge base. 
    * 2 resources - 1 for test, 1 for production: have two QnA Maker resources, using one for testing (with its own test and  production indexes) and one for product (also having its own test and production indexes)
* and, always use the same parameters, such as **[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** when querying both your test and production knowledge base

При публикации базы знаний ее содержимое раздела вопросов и ответов переносится из тестового индекса в рабочий индекс в службе "Поиск Azure". См. [здесь](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base), чтобы узнать, как работает процесс публикации.

If you have a knowledge base in different regions, each region uses its own Azure Cognitive Search index. Так как используются разные индексы, оценки не обязательно будут совпадать. 


## <a name="no-match-found"></a>Совпадения не найдены
Если ни одно из подходящих совпадений не найдено ранжировщиком, возвращается оценка достоверности 0.0 или "Нет", а ответ по умолчанию – "Нет хорошего соответствия в базе знаний". You can override this [default response](#change-default-answer) in the bot or application code calling the endpoint. В качестве альтернативы вы также можете установить переопределение ответа в Azure, и это изменит значение по умолчанию для всех баз знаний, развернутых в определенной службе QnA Maker.

## <a name="change-default-answer"></a>Изменение ответа по умолчанию

1. Перейдите на [портал Azure](https://portal.azure.com) и перейдите к группе ресурсов, которая представляет созданный вами сервис QnA Maker.

2. Щелкните, чтобы открыть **службы приложений**.

    ![Доступ к службе приложений для QnA Maker на портале Azure](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Щелкните **Параметры приложения** и укажите в поле **DefaultAnswer** требуемый ответ по умолчанию. В нижней части страницы нажмите кнопку **Save**.

    ![Выбор элемента "Параметры приложения" и изменение значения в поле DefaultAnswer для QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Перезапустите службу приложений

    ![Перезапуск службы приложений QnA Maker после изменения значения в поле DefaultAnswer](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Поддерживаемые источники данных](./data-sources-supported.md)

