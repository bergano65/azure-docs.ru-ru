---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью REST (Node.js)
description: В этом кратком руководстве по Node.js для REST описывается, как программным способом получить ответ из базы знаний.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 9031c320b055c0a7191655d6531eaa31d9cc8bb8
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851732"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Краткое руководство. получение ответов на вопрос из базы знаний с помощью Node. js

В этом кратком руководстве объясняется, как получить ответ из базы знаний QnA Maker программными средствами. База знаний содержит вопросы и ответы из [источника данных](../Concepts/knowledge-base.md), например часто задаваемые вопросы. [Вопросы](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) отправляются в службу QnA Maker. Результат содержит прогнозируемый [ответ](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties).

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Пример](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>Предварительные требования

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ, выберите **Ключи** в разделе **Управление ресурсами** на панели мониторинга ресурса QnA Maker на портале Azure.
* Параметры страницы **Публикация**. Если у вас нет опубликованной базы знаний, создайте новую, импортируйте ее на странице **Параметры**, а затем опубликуйте ее. Можно скачать и использовать [эту простую базу знаний](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    На странице параметров публикации представлены значения Host, EndpointKey и маршрута POST.

    ![Опубликовать параметры](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Создание файла Node.js

Создайте в VSCode файл с именем `get-answer.js`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

В верхней части файла `get-answer.js` добавьте необходимые зависимости в проект:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант

Затем добавьте необходимые константы для обеспечения доступа к QnA Maker. Эти значения отобразятся на странице **публикации** после публикации базы знаний.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Добавление запроса POST для отправки вопроса и получения ответа

Следующий код создает HTTPS-запрос к API службы QnA Maker, чтобы отправить вопрос в базу знаний и получить ответ.

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

Значение заголовка `Authorization` содержит строку `EndpointKey`.

## <a name="install-the-dependencies"></a>Установка зависимостей

Установите зависимости из командной строки:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Запуск программы

Выполните запуск программы из командной строки. Она автоматически отправит запрос к API службы QnA Maker, а полученный ответ отобразится в окне консоли.

Запустите файл.

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Дополнительные сведения о [запросе](../how-to/metadata-generateanswer-usage.md#generateanswer-request) и [ответе](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))