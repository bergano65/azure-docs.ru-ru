---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью REST (C#)
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве по C# для REST описывается, как программным способом получить ответ из базы знаний.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: f2b5089f1ec334f80ccb4ce80ba9b512f3305859
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861125"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Получение ответов на вопрос из базы знаний с помощью C#

В этом кратком руководстве объясняется, как получить ответ из базы знаний QnA Maker программными средствами. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/data-sources-supported.md). Вопрос в формате JSON отправляется в тексте запроса API. 


## <a name="prerequisites"></a>Предварительные требования

* Последний [**выпуск Visual Studio Community**](https://www.visualstudio.com/downloads/).
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ, выберите **Ключи** в разделе **Управление ресурсами** на панели мониторинга ресурса QnA Maker на портале Azure. 
* Параметры страницы **Публикация**. Если у вас нет опубликованной базы знаний, создайте новую, импортируйте ее на странице **Параметры**, а затем опубликуйте ее. Можно скачать и использовать [эту простую базу знаний](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    На странице параметров публикации представлены значения Host, EndpointKey и маршрута POST. 

    ![Параметры публикации](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Код для этого краткого руководства доступен в репозитории [https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-knowledge-base-project"></a>Создание проекта базы знаний

1. Откройте Visual Studio 2017 Community.
1. Создайте проект консольного приложения (.Net Core) и присвойте ему имя QnaMakerQuickstart. Для остальных параметров оставьте значения по умолчанию.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

В начале файла Program.cs замените одиночный оператор using следующими строками, чтобы добавить необходимые зависимости в проект:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант

В верхней части класса `Program` в методе `Main` добавьте необходимые константы для обеспечения доступа к QnA Maker. Эти значения будут доступны на странице **Публикация** после публикации базы знаний. 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=14-30 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Добавление запроса POST для отправки вопроса и получения ответа

Следующий код выполняет HTTPS-запрос к API службы QnA Maker, чтобы отправить вопрос в базу знаний и получить ответ:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=32-57 "Add a POST request to send question to knowledge base")]

Значение заголовка `Authorization` содержит строку `EndpointKey `. 

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Выполните сборку и запуск программы из Visual Studio. Она автоматически отправит запрос к API службы QnA Maker, а полученный ответ отобразится в окне консоли.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))
