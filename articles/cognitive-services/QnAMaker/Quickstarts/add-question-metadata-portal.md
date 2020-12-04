---
title: Добавление вопросов и ответов на портале QnA Maker
description: В этой статье описано, как добавить пары вопросов и ответов с метаданными, чтобы пользователи могли найти правильный ответ на свой вопрос.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 53c0afa1cdb8c9920875b7ba694339107714bd54
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462198"
---
# <a name="add-questions-and-answer-with-qna-maker-portal"></a>Добавление вопросов и ответов с помощью портала QnA Maker

После создания базы знаний добавьте пары вопросов и ответов (QnA) с метаданными, чтобы отфильтровать ответ. В приведенной ниже таблице собраны вопросы о лимитах для служб Azure, относящиеся к разным службам поиска Azure.

<a name="qna-table"></a>

|Пара|Вопросы|Ответ|Метаданные|
|--|--|--|--|
|1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

После добавления метаданных в пару вопросов и ответов клиентское приложение получит следующие возможности:

* запрашивать ответы с определенными значениями метаданных;
* получать все ответы и дополнительно обрабатывать их с учетом метаданных каждого ответа.


## <a name="prerequisites"></a>Предварительные требования

* Выполните инструкции, приведенные в [предыдущем кратком руководстве](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Входа на портал QnA Maker

1. Войдите на [портал QnA Maker](https://www.qnamaker.ai).

1. Выберите существующую базу знаний из [предыдущего краткого руководства](./create-publish-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Добавление дополнительных вопросов с другими формулировками

В текущей базе знаний есть пары вопросов и ответов по устранению неполадок в QnA Maker. Эти пары были созданы при добавлении URL-адреса в базу знаний в процессе создания.

При импорте этого URL-адреса был создан только один вопрос с одним ответом. В этой процедуре вы добавите дополнительные вопросы.

1. На странице **Правка** найдите вопрос `How large a knowledge base can I create?` с помощью текстового поля поиска над парами вопросов и ответов.

1. В столбце **Вопрос** выберите **+ Add alternative phrasing** (Добавить альтернативные выражения) и поочередно введите все новые формулировки, которые представлены в следующей таблице.

    |Альтернативные выражения|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Щелкните **Save and train** (Сохранить и обучить), чтобы заново обучить базу знаний.

1. Щелкните **Тест** и введите вопрос, формулировка которого близка к любому из новых альтернативных выражений, но не точно с ним совпадает.

    `What GB size can a knowledge base be?`

    Вы получите правильный ответ в формате разметки Markdown:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`

    Щелкнув команду **Inspect** (Проверить) под возвращенным ответом, вы увидите дополнительные ответы, которые подходят к этому вопросу с меньшим уровнем достоверности.

    Нет необходимости добавлять все возможные сочетания формулировок и выражений. При включении функции [активного обучения](../how-to/improve-knowledge-base.md) в QnA Maker, он автоматически подбирает альтернативные выражения для оптимального соответствия базы знаний потребностям ваших пользователей.

1. Повторно щелкните **Тест**, чтобы закрыть окно тестирования.

## <a name="add-metadata-to-filter-the-answers"></a>Добавление метаданных для фильтрации ответов

Добавление метаданных в пары вопросов и ответов позволяет клиентскому приложению получать ответы с фильтрацией. Фильтр применяется перед применением [первого и второго ранжирования](../concepts/query-knowledge-base.md#ranker-process).

1. Добавьте вторую пару вопросов и ответов без метаданных из [первой таблицы в этом кратком руководстве](#qna-table), а затем выполните следующие действия.

1. Щелкните **Параметры просмотра**, а затем **Показать метаданные**.

1. Для пары вопросов и ответов, которую вы только что добавили, выберите команду **Добавить теги метаданных**, а затем укажите имя `service` и значение `search`. Это выглядит следующим образом: `service:search`.

1. Добавьте еще один тег метаданных с именем `link_in_answer` и значением `false`. Это выглядит следующим образом: `link_in_answer:false`.

1. Выполните поиск первого ответа `How large a knowledge base can I create?` в таблице.

1. Добавьте пары метаданных для тех же двух тегов метаданных:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

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
