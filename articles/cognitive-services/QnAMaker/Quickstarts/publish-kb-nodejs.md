---
title: Публикация базы знаний с использованием REST и Node.js
titleSuffix: QnA Maker - Azure Cognitive Services
description: В этом кратком руководстве по Node.js описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний отправляется в индекс выделенной службы "Поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 915d5223b99f530978ade41f69420766cf591021
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787906"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>Краткое руководство. Публикация базы знаний в QnA Maker с использованием Node.js

В этом кратком руководстве по REST описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний отправляется в индекс выделенной службы "Поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Публикация.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) При использовании этого API в тексте запроса не нужно указывать какие-либо сведения.

## <a name="prerequisites"></a>Предварительные требования

* [Node.js 6+](https://nodejs.org/en/download/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить этот ключ, выберите **Ключи** в разделе **Управление ресурсами** на информационной панели. 
* Идентификатор базы знаний QnA Maker, который находится в URL-адресе в параметре строки запроса kbid, как показано ниже.

    ![Идентификатор базы знаний QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Если у вас еще нет базы знаний, ее можно создать для этого руководства. [Создавать базу знаний](create-new-kb-nodejs.md).


> [!NOTE] 
> Полные файлы решения доступны в [репозитории GitHub **Azure-Samples/cognitive-services-qnamaker-nodejs**](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short).

## <a name="create-a-knowledge-base-nodejs-file"></a>Создание файла базы знаний Node.js

Создайте файл с именем `publish-knowledge-base.js`.

## <a name="add-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `publish-knowledge-base.js` укажите следующие строки:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>Добавление необходимых констант

После указания зависимостей добавьте необходимые константы, чтобы получить доступ к QnA Maker. Вместо примеров подставьте собственные значения.

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Добавление запроса POST для публикации базы знаний

Включив константы, добавьте следующий код, который отправляет HTTPS-запрос к API службы QnA Maker, чтобы опубликовать базу знаний и получить ответ:

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

Вызов API возвращает состояние 204 для успешной публикации без содержимого в тексте ответа. Код добавляет содержимое для ответов 204.

Этот ответ возвращается без изменений для любого другого ответа.

## <a name="run-the-program"></a>Запуск программы

Выполните сборку и запуск программы. Эта программа автоматически отправляет запрос к API службы QnA Maker на публикацию базы знаний. Ответ выводится в окно консоли.

После публикации базы знаний вы можете запросить ее из конечной точки с помощью клиентского приложения или чат-бота. 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Дополнительная информация

Опубликовав базу знаний, вам нужно использовать [URL-адрес конечной точки для создания ответа](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))
