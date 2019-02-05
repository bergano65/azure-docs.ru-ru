---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью REST (Python)
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве по Python для REST описывается, как программным способом получить ответ из базы знаний.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 0647d850491f31d0f66de18a593ac7ff471e8906
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206414"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Получение ответов на вопрос из базы знаний с помощью Python

В этом кратком руководстве объясняется, как получить ответ из базы знаний QnA Maker программными средствами. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/data-sources-supported.md). Вопрос в формате JSON отправляется в тексте запроса API. 

## <a name="prerequisites"></a>Предварительные требования

* [Python версии 3.6 или выше](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ, выберите **Ключи** в разделе **Управление ресурсами** на панели мониторинга ресурса QnA Maker на портале Azure. 
* Параметры страницы **Публикация**. Если у вас нет опубликованной базы знаний, создайте новую, импортируйте ее на странице **Параметры**, а затем опубликуйте ее. Можно скачать и использовать [эту простую базу знаний](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    На странице параметров публикации представлены значения Host, EndpointKey и маршрута POST. 

    ![Параметры публикации](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Код для этого краткого руководства доступен в репозитории [https://github.com/Azure-Samples/cognitive-services-qnamaker-python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-python-file"></a>Создание файла Python

Создайте в VSCode файл с именем `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

В верхней части файла `get-answer-3x.py` добавьте необходимые зависимости в проект:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

Узел и маршрут отличаются от отображаемых на странице **Публикация**, Так как библиотека python не разрешает использовать маршрутизацию на узле. Маршрутизация, которая отображается на странице **Публикация** как часть узла, перемещена в маршрут.

## <a name="add-the-required-constants"></a>Добавление необходимых констант

Добавьте необходимые константы для обеспечения доступа к QnA Maker. Эти значения будут доступны на странице **Публикация** после публикации базы знаний. 

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Добавление запроса POST для отправки вопроса и получения ответа

Следующий код выполняет HTTPS-запрос к API службы QnA Maker, чтобы отправить вопрос в базу знаний и получить ответ:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

Значение заголовка `Authorization` содержит строку `EndpointKey `. 

## <a name="run-the-program"></a>Запуск программы

Выполните запуск программы из командной строки. Запрос к API службы QnA Maker будет отправлен автоматически, а полученный ответ отобразится в окне консоли.

Запустите файл.

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))
