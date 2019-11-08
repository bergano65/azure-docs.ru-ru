---
title: Краткое руководство. Добавление вопросов и ответов на портале QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 92735d8982fb1364d5ebfe0494f5ee51f4302469
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794007"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Краткое руководство. Добавление вопросов и ответов с помощью портала QnA Maker

После создания базы знаний добавьте наборы вопросов и ответов с метаданными, чтобы пользователи могли найти правильный ответ на свой вопрос.

Правильным считается один конкретный ответ, но у пользователей есть много способов задать вопрос, ведущий к этому ответу.

Например, в следующей таблице собраны вопросы о лимитах для служб Azure, относящиеся к разным службам Azure. 

<a name="qna-table"></a>


|Set|Вопросы|Ответ|Метаданные|
|--|--|--|--|
|1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

После добавления метаданных в набор вопросов и ответов клиентское приложение получит следующие возможности:

* запрашивать ответы с определенными значениями метаданных;
* получать все ответы и дополнительно обрабатывать их с учетом метаданных каждого ответа.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования

* Служба QnA Maker
* База знаний, созданная в этой службе QnA Maker

Оба этих объекта мы создали в [первом кратком руководстве.](../how-to/create-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Входа на портал QnA Maker

1. Войдите на [портал QnA Maker](https://www.qnamaker.ai).

1. Выберите существующую базу знаний. Если у вас нет базы знаний, вернитесь к [предыдущему краткому руководству](../how-to/create-knowledge-base.md) и выполните процедуру по созданию базы знаний.

## <a name="add-additional-alternatively-phrased-questions"></a>Добавление дополнительных вопросов с другими формулировками 

В текущей базе знаний, которую мы создали [в предыдущем кратком руководстве](../how-to/create-knowledge-base.md), сохранены наборы вопросов и ответов QnA Maker по устранению неполадок. Эти наборы были созданы при добавлении URL-адреса в базу знаний в процессе создания. 

При импорте этого URL-адреса был создан только один вопрос с одним ответом. 

В этой процедуре вы добавите дополнительные вопросы.

1. На странице **Правка** найдите вопрос `How large a knowledge base can I create?` с помощью текстового поля поиска над наборами вопросов и ответов.

1. В столбце **Вопрос** выберите **+ Add alternative phrasing** (Добавить альтернативные выражения) и поочередно введите все новые формулировки, которые представлены в следующей таблице.

    |Альтернативные выражения|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. Щелкните **Save and train** (Сохранить и обучить), чтобы заново обучить базу знаний. 

1. Щелкните **Тест** и введите вопрос, формулировка которого близка к любому из новых альтернативных выражений, но не точно с ним совпадает.

    `What GB size can a knowledge base be?`

    Вы получите правильный ответ в формате разметки Markdown: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Щелкнув команду **Inspect** (Проверить) под возвращенным ответом, вы увидите дополнительные ответы, которые подходят к этому вопросу с меньшим уровнем достоверности. 

    Нет необходимости добавлять все возможные сочетания формулировок и выражений. Включите функцию [активного обучения](../how-to/improve-knowledge-base.md) в QnA Maker, которая автоматически подбирает альтернативные выражения для оптимального соответствия базы знаний потребностям ваших пользователей.

1. Повторно щелкните **Тест**, чтобы закрыть окно тестирования.

## <a name="add-metadata-to-filter-the-answers"></a>Добавление метаданных для фильтрации ответов

Добавление метаданных в набор вопросов и ответов позволяет клиентскому приложению получать ответы с фильтрацией. Фильтр применяется перед применением [первого и второго ранжирования](../concepts/knowledge-base.md#ranker-process).

1. Добавьте второй набор вопросов и ответов без метаданных из [первой таблицы в этом кратком руководстве](#qna-table), а затем выполните следующие действия. 

1. Щелкните **Параметры просмотра**, а затем **Показать метаданные**. 

1. Для набора вопросов и ответов, который вы только что добавили, выберите команду **Add metadata tags** (Добавить теги метаданных), а затем укажите имя `service` и значение `search`, `service:search`.

1. Добавьте еще один тег метаданных с именем `link_in_answer` и значением `false`, `link_in_answer:false`.

1. Выполните поиск первого ответа `How large a knowledge base can I create?` в таблице. 
1. Добавьте пары метаданных для тех же двух тегов метаданных:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Теперь у вас есть два вопроса, у которых есть одинаковые теги метаданных с разными значениями. 

1. Щелкните **Save and train** (Сохранить и обучить), чтобы заново обучить базу знаний. 

1. Чтобы перейти к странице публикации, щелкните **Publish** (Опубликовать). 
1. Нажмите кнопку **Опубликовать**, чтобы опубликовать текущую базу знаний в конечной точке, которая будет обрабатывать запросы. 
1. После публикации базы знаний перейдите на вкладку **Curl**, чтобы увидеть пример команды cURL для создания ответа из базы знаний.
1. Скопируйте эту команду в блокнот или любой другой редактор, чтобы изменить ее. Введите реальные значения имени ресурса, идентификатора базы знаний и ключа конечной точки:

    |Замените|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Обратите внимание, что вопрос состоит всего из одного слова (`size`), по которому может возвращаться любой набор вопросов и ответов. Массив `strictFilters` указывает, что ответ нужно сократить до `qna_maker` ответов. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. Ответ включает только тот ответ, который соответствует критериям фильтра. 

    Следующий ответ cURL был отформатирован для удобства чтения:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Если существует набор вопросов и ответов, который не соответствует поисковому запросу, но соответствует фильтру, он не будет возвращен. Вместо этого возвращается общий ответ `No good match found in KB.`.

    Убедитесь, что пары "имя — значение" в метаданных не превышают существующие ограничения. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Получение ответа с помощью Postman](get-answer-from-kb-using-postman.md)