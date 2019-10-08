---
title: Краткое руководство. Клиентская библиотека QnA Maker для Node.js
titleSuffix: Azure Cognitive Services
description: Приступая к работе с клиентской библиотекой QnA Maker для Node.js. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: ab3fa02f7285fdbde918f9e365d1ee96ba5e71a1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802994"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Краткое руководство. Клиентская библиотека QnA Maker для Node.js

Приступая к работе с клиентской библиотекой QnA Maker для Node.js. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам. 

Клиентская библиотека QnA Maker для Node.js:

* Создание базы знаний 
* Управление базой знаний
* Публикация базы знаний

[Справочная документация](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Пакет (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Примеры Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Настройка

### <a name="create-a-qna-maker-azure-resource"></a>Создание ресурса QnA Maker Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс с помощью [Create a Cognitive Services account using the Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)(создания учетной записи Cognitive Services с помощью портала Azure) или [Create a Cognitive Services account using the Azure Command-Line Interface(CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) (создания учетной записи Cognitive Services с помощью интерфейса командной строки Azure (CLI)) на локальном компьютере. 

После получения ключа от своего ресурса [создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ресурса с именами `QNAMAKER_SUBSCRIPTION_KEY` и `QNAMAKER_HOST`. Используйте значения ключа и конечной точки, найденные на страницах **Ключи** и **Обзор** ресурса на портале Azure.

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init -y`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init -y
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите пакеты NPM: `ms-rest-azure` и `azure-cognitiveservices-qnamaker`.

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Файл `package.json` приложения обновлен с учетом зависимостей.


## <a name="object-model"></a>Объектная модель

Клиент QnA Maker — это объект [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest), который проходит проверку подлинности в Azure с помощью объекта ServiceClientCredentials, который содержит ваш ключ.

После создания клиента используйте [свойство QnAMakerClient.Knowledgebase](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) для создания, управления и публикации своей базы знаний. 

Управляйте своей базой знаний, отправляя объект JSON. Для немедленных операций метод обычно возвращает объект JSON, указав статус. Ответ для длительных операций — ИД операции. Вызовите метод [client.Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) с идентификатором операции, чтобы определить [состояние запроса](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest). 

 
## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки QnA Maker для Node.js:

* [Создание базы знаний](#create-a-knowledge-base)
* [Обновление базы знаний](#update-a-knowledge-base)
* [Публикация базы знаний](#publish-a-knowledge-base)
* [Удаление базы знаний](#delete-a-knowledge-base)
* [Получение состояния операции](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Добавление зависимостей

Создайте файл с именем `index.js`. Добавьте в файл библиотеку QnA Maker и библиотеку Azure REST.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Создайте переменные для конечной точки Azure и ключа ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

|Переменная среды|Переменная Node.js|Пример|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|32-символьный GUID|
|`QNAMAKER_HOST`|`endpoint`|`https://your-resource-name.api.cognitive.microsoft.com` —замените поддомен `your-resource-name` значением имени ресурса.|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Затем создайте объект ServiceClientCredentials с ключом и используйте его с конечной точкой, чтобы создать объект [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-). Используйте объект клиента, чтобы получить объект [базы знаний](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest).


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest).
* Для **файлов** используйте объект [Класс FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest). 
* Для **URL-адресов** используйте список строк.

Вызовите метод[Создать](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--), а затем передайте возвращенный идентификатор операции методу [Operations.getDetails](#get-status-of-an-operation) для опроса состояния. 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

Чтобы создать базу знаний, добавьте функцию [`wait_for_operation`](#get-status-of-an-operation) из предыдущего примера кода. 

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Базу знаний можно обновить, передав идентификатор базы знаний и метод [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest), содержащий [добавить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [обновить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) и [удалить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) объекты DTO в метод [обновить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-). Используйте метод [Operation.getDetail](#get-status-of-an-operation), чтобы определить, успешно ли выполнено обновление.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

Чтобы обновить базу знаний, добавьте функцию [`wait_for_operation`](#get-status-of-an-operation) из предыдущего примера кода. 

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [опубликовать](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется ее в конечной точке. 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний с помощью метода [удалить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) с параметром идентификатора базы знаний. 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest). Используйте [свойство Operation.OperationId](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода. 

Вызов _setTimeout_ в следующем блоке кода используется для имитации асинхронного кода. Замените это на логику повторных попыток. 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с командой `node index.js` из каталога приложения.


Все фрагменты кода из этой статьи [доступны для скачивания](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) и могут выполняться как один файл.

```console
node index.js
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Руководство. Создание базы знаний и ответ на вопрос](../tutorials/create-publish-query-in-portal.md)

* [Что такое QnA Maker?](../Overview/overview.md)
* [Редактирование базы знаний](../how-to/edit-knowledge-base.md)
* [Get analytics on your knowledge base](../how-to/get-analytics-knowledge-base.md) (Получение аналитики по своей базе знаний)