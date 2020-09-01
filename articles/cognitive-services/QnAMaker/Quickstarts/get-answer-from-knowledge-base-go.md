---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью REST (Go)
description: В этом кратком руководстве по Go для REST описывается, как программным способом получить ответ из базы знаний.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: f9d9b995f8e78ec91bd26c31dde16b97188f0371
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269188"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-go"></a>Краткое руководство. получение ответов на вопрос из базы знаний с помощью Go

В этом кратком руководстве объясняется, как получить ответ из базы знаний QnA Maker программными средствами. База знаний содержит вопросы и ответы из [источника данных](../Concepts/knowledge-base.md), например часто задаваемые вопросы. [Вопросы](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) отправляются в службу QnA Maker. Результат содержит прогнозируемый [ответ](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties).

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Пример](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/get-answer/get-answer.go)

## <a name="prerequisites"></a>Предварительные требования

* [Go 1.10.1.](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ, выберите **Ключи** в разделе **Управление ресурсами** на панели мониторинга ресурса QnA Maker на портале Azure.
* Параметры страницы **Публикация**. Если у вас нет опубликованной базы знаний, создайте новую, импортируйте ее на странице **Параметры**, а затем опубликуйте ее. Можно скачать и использовать [эту простую базу знаний](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    На странице параметров публикации представлены значения Host, EndpointKey и маршрута POST.

    ![Параметры публикации](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-go-file"></a>Создание файла Go

Откройте VSCode, создайте файл с именем `get-answer.go` и добавьте следующий класс:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Над функцией `main` в верхней части файла `get-answer.go` добавьте необходимые зависимости в проект:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/query-kb.go" id="dependencies":::

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Добавление запроса POST для отправки вопроса и получения ответа

Следующий код создает HTTPS-запрос к API службы QnA Maker, чтобы отправить вопрос в базу знаний и получить ответ.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/query-kb.go" id="main":::

Значение заголовка `Authorization` содержит строку `EndpointKey`.

Дополнительные сведения о [запросе](../how-to/metadata-generateanswer-usage.md#generateanswer-request) и [ответе](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Выполните сборку и запуск программы из командной строки. Она автоматически отправит запрос к API службы QnA Maker, а полученный ответ отобразится в окне консоли.

1. Выполните сборку файла:

    ```bash
    go build get-answer.go
    ```

1. Запустите файл.

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))
