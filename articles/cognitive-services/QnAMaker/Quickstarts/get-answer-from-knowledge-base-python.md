---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью REST (Python)
description: В этом кратком руководстве по Python для REST описывается, как программным способом получить ответ из базы знаний.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, tracking-python
ms.topic: how-to
ms.openlocfilehash: d985bafe3303acdaf8b47e70a9d5368e3b26335c
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610140"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Краткое руководство. получение ответов на вопрос из базы знаний с помощью Python

В этом кратком руководстве объясняется, как получить ответ из базы знаний QnA Maker программными средствами. База знаний содержит вопросы и ответы из [источника данных](../Concepts/knowledge-base.md), например часто задаваемые вопросы. [Вопросы](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) отправляются в службу QnA Maker. Результат содержит прогнозируемый [ответ](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties).

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Пример](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Предварительные требования

* [Python версии 3.6 или выше.](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ, выберите **Ключи** в разделе **Управление ресурсами** на панели мониторинга ресурса QnA Maker на портале Azure.
* Параметры страницы **Публикация**. Если у вас нет опубликованной базы знаний, создайте новую, импортируйте ее на странице **Параметры**, а затем опубликуйте ее. Можно скачать и использовать [эту простую базу знаний](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    На странице параметров публикации представлены значения Host, EndpointKey и маршрута POST.

    ![Параметры публикации](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Создание файла Python

Создайте в VSCode файл с именем `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

В верхней части файла `get-answer-3x.py` добавьте необходимые зависимости в проект:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

<!--TBD - reword this following paragraph -->

Узел и маршрут отличаются от отображаемых на странице **Публикация**, Так как библиотека python не разрешает использовать маршрутизацию на узле. Маршрутизация, которая отображается на странице **Публикация** как часть узла, перемещена в маршрут.

## <a name="add-the-required-constants"></a>Добавление необходимых констант

Добавьте необходимые константы для обеспечения доступа к QnA Maker. Эти значения отобразятся на странице **публикации** после публикации базы знаний.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Добавление запроса POST для отправки вопроса и получения ответа

Следующий код создает HTTPS-запрос к API службы QnA Maker, чтобы отправить вопрос в базу знаний и получить ответ.

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

Значение заголовка `Authorization` содержит строку `EndpointKey`.

## <a name="run-the-program"></a>Запуск программы

Выполните запуск программы из командной строки. Она автоматически отправит запрос к API службы QnA Maker, а полученный ответ отобразится в окне консоли.

Запустите файл.

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Дополнительные сведения о [запросе](../how-to/metadata-generateanswer-usage.md#generateanswer-request) и [ответе](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))
