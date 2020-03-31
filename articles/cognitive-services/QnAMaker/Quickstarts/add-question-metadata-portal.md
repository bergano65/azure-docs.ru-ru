---
title: Краткое руководство. Добавление вопросов и ответов на портале QnA Maker
description: В этом кратком руководстве описано, как добавить наборы вопросов и ответов с метаданными, чтобы пользователи могли найти правильный ответ на свой вопрос.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 25c0fe549dfc850a53b06f79f348a87cba3b70a1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109930"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Краткое руководство. Добавление вопросов и ответов с помощью портала QnA Maker

После создания базы знаний добавьте наборы вопросов и ответов (QnA) с метаданными, чтобы отфильтровать ответ. В следующей таблице собраны вопросы о лимитах для служб Azure, относящиеся к разным службам Azure.

<a name="qna-table"></a>

|Присвойте параметру|Вопросы|Ответ|Метаданные|
|--|--|--|--|
|1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

После добавления метаданных в набор вопросов и ответов клиентское приложение получит следующие возможности:

* запрашивать ответы с определенными значениями метаданных;
* получать все ответы и дополнительно обрабатывать их с учетом метаданных каждого ответа.


## <a name="prerequisites"></a>Предварительные требования

* Выполните инструкции, приведенные в [предыдущем кратком руководстве](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Входа на портал QnA Maker

1. Войдите на [портал QnA Maker](https://www.qnamaker.ai).

1. Выберите существующую базу знаний из [предыдущего краткого руководства](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Добавление дополнительных вопросов с другими формулировками

В текущей базе знаний есть наборы вопросов и ответов по устранению неполадок в QnA Maker. Эти наборы были созданы при добавлении URL-адреса в базу знаний в процессе создания.

При импорте этого URL-адреса был создан только один вопрос с одним ответом. В этой процедуре вы добавите дополнительные вопросы.

1. На странице **Правка** найдите вопрос `How large a knowledge base can I create?` с помощью текстового поля поиска над наборами вопросов и ответов.

1. В столбце **Вопрос** выберите **+ Add alternative phrasing** (Добавить альтернативные выражения) и поочередно введите все новые формулировки, которые представлены в следующей таблице.

    |Альтернативные выражения|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Щелкните **Save and train** (Сохранить и обучить), чтобы заново обучить базу знаний.

1. Щелкните **Тест** и введите вопрос, формулировка которого близка к любому из новых альтернативных выражений, но не точно с ним совпадает.

    `What GB size can a knowledge base be?`

    Вы получите правильный ответ в формате разметки Markdown:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Щелкнув команду **Inspect** (Проверить) под возвращенным ответом, вы увидите дополнительные ответы, которые подходят к этому вопросу с меньшим уровнем достоверности.

    Нет необходимости добавлять все возможные сочетания формулировок и выражений. При включении функции [активного обучения](../how-to/improve-knowledge-base.md) в QnA Maker, он автоматически подбирает альтернативные выражения для оптимального соответствия базы знаний потребностям ваших пользователей.

1. Повторно щелкните **Тест**, чтобы закрыть окно тестирования.

## <a name="add-metadata-to-filter-the-answers"></a>Добавление метаданных для фильтрации ответов

Добавление метаданных в набор вопросов и ответов позволяет клиентскому приложению получать ответы с фильтрацией. Фильтр применяется перед применением [первого и второго ранжирования](../concepts/query-knowledge-base.md#ranker-process).

1. Добавьте второй набор вопросов и ответов без метаданных из [первой таблицы в этом кратком руководстве](#qna-table), а затем выполните следующие действия.

1. Щелкните **Параметры просмотра**, а затем **Показать метаданные**.

1. Для набора вопросов и ответов, который вы только что добавили, выберите команду **Add metadata tags** (Добавить теги метаданных), а затем укажите имя `service` и значение `search`. Это выглядит следующим образом: `service:search`.

1. Добавьте еще один тег метаданных с именем `link_in_answer` и значением `false`. Это выглядит следующим образом: `link_in_answer:false`.

1. Выполните поиск первого ответа `How large a knowledge base can I create?` в таблице.

1. Добавьте пары метаданных для тех же двух тегов метаданных:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Теперь у вас есть два вопроса, у которых есть одинаковые теги метаданных с разными значениями.

1. Щелкните **Save and train** (Сохранить и обучить), чтобы заново обучить базу знаний.

1. Чтобы перейти к странице публикации, щелкните **Publish** (Опубликовать).
1. Нажмите кнопку **Опубликовать**, чтобы опубликовать текущую базу знаний в конечной точке.
1. После публикации базы знаний перейдите к следующему краткому руководству, чтобы узнать, как создать ответ из базы знаний.

## <a name="what-did-you-accomplish"></a>Что вы сделали

Вы изменили базу знаний для поддержки дополнительных вопросов и предоставленных пар "имя-значение" для поддержки фильтрации во время поиска наилучшего ответа или последующей обработки после возврата ответа или ответов.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не переходите к следующему краткому руководству, удалите ресурсы платформы QnA Maker и Bot на портале Azure.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Quickstart: Get an answer from knowledge base](get-answer-from-knowledge-base-using-url-tool.md) (Краткое руководство. Получение ответа из базы знаний)