---
title: Краткое руководство. Публикация базы знаний QnA Maker с использованием Node.js и REST
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний отправляется в индекс выделенной службы "Поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: diberry
ms.openlocfilehash: c70b90a6e465c72193f63afd7ab9106579e2c634
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886616"
---
# <a name="quickstart-publish-a-qna-maker-knowledge-base-in-nodejs"></a>Краткое руководство. Публикация базы знаний QnA Maker с использованием Node.js

В этом кратком руководстве описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний отправляется в индекс выделенной службы "Поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Публикация.](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) При использовании этого API в тексте запроса не нужно указывать какие-либо сведения.

## <a name="prerequisites"></a>Предварительные требования

* [Node.js 6+](https://nodejs.org/en/download/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить этот ключ, выберите **Ключи** в разделе **Управление ресурсами** на информационной панели. 
* Идентификатор базы знаний QnA Maker, который находится в URL-адресе в параметре строки запроса kbid, как показано ниже.

    ![Идентификатор базы знаний QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Если у вас еще нет базы знаний, создайте пример для этого краткого руководства по инструкциям из [этой статьи](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Создание файла базы знаний Node.js

Создайте файл с именем `publish-knowledge-base.js`.

## <a name="add-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `publish-knowledge-base.js` укажите следующие строки:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Добавление необходимых констант

После указания зависимостей добавьте необходимые константы, чтобы получить доступ к QnA Maker. Замените значение переменной `subscriptionKey` собственным ключом QnA Maker. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Добавление идентификатора базы знаний

После определения констант далее необходимо добавить идентификатор базы знаний и указать его в пути:

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-supporting-functions"></a>Добавление вспомогательных функций

Далее необходимо добавить приведенные ниже вспомогательные функции.

1. Добавьте следующую функцию, чтобы распечатать файл JSON в доступном для чтения формате:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=25-28 "Add supporting functions, step 1")]

2. Добавьте следующие функции, чтобы управлять HTTP-ответом и получить состояние операции создания:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=30-52 "Add supporting functions, step 2")]

## <a name="add-the-publishkb-function-and-main-function"></a>Добавление функции publish_kb и функции main

Приведенный ниже код отправляет HTTPS-запрос к API службы QnA Maker на публикацию базы знаний. Возвращается следующий ответ:

[!code-nodejs[Add POST request to publish KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=54-71 "Add POST request to publish KB")]

[!code-nodejs[Add the publish_kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=73-91 "Add the publish_kb function and main function")]

## <a name="add-the-main-function"></a>Добавление функции main

Добавьте следующую функцию, чтобы управлять запросом и ответом:

[!code-nodejs[Add the main function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=94-97 "Add the main function")]

## <a name="run-the-program"></a>Запуск программы

Выполните сборку и запуск программы. Эта программа автоматически отправляет запрос к API службы QnA Maker на публикацию базы знаний. Ответ выводится в окно консоли.

После публикации базы знаний вы можете запросить ее из конечной точки с помощью клиентского приложения или чат-бота. 

```bash
node publish-knowledge-base.js
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))