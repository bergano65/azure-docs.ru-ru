---
title: Краткое руководство. Клиентская библиотека QnA Maker для Node.js
description: В этом кратком руководстве показано, как начать работу с клиентской библиотекой QnA Maker для Node.js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020954"
---
Клиентская библиотека QnA Maker для Node.js:

* Создание базы знаний
* Обновление базы знаний
* Публикация базы знаний
* Получение опубликованного ключа конечной точки
* Ожидание долго выполняющейся задачи
* Удаление базы знаний

[Справочная документация](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Пакет (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Примеры Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Настройка

### <a name="create-a-qna-maker-azure-resource"></a>Создание ресурса QnA Maker Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс с помощью [Create a Cognitive Services account using the Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)(создания учетной записи Cognitive Services с помощью портала Azure) или [Create a Cognitive Services account using the Azure Command-Line Interface(CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) (создания учетной записи Cognitive Services с помощью интерфейса командной строки Azure (CLI)) на локальном компьютере.

После получения ключа и конечной точки из ресурса получите значения из портала Azure для нового ресурса на странице быстрого запуска.

[Создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication), именованные `QNAMAKER_AUTHORING_KEY` и `QNAMAKER_ENDPOINT`. Вы можете скопировать файл [.env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) в `.env` и использовать переменные среды в этом файле.

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Выполните команду `npm init -y`, чтобы создать приложение узла с помощью файла `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите обязательные и необязательные пакеты NPM:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

Файл `package.json` приложения обновлен с учетом зависимостей. `dotenv` является необязательным и используется для задания переменных среды в текстовом файле. Не проверяйте `.env` в системе управления версиями.


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
* [Получение опубликованной конечной точки](#get-published-endpoint)
* [Получение состояния операции](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Добавление зависимостей

Создайте файл с именем `index.js`. Добавьте в файл библиотеку QnA Maker и зависимости.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Создайте переменные для конечной точки Azure и ключа ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

|Переменная среды|Переменная Node.js|Пример|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|Ключ — это строка из 32 символов, доступная на странице "Быстрый запуск" ресурса QnA Maker на портале Azure. Это не то же самое, что ключ конечной точки прогнозирования.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Конечная точка разработки (в формате `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`) содержит **имя ресурса**. Это не тот же URL-адрес, который используется для запроса конечной точки прогнозирования.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Затем создайте объект ApiKeyCredentials с ключом и используйте его с конечной точкой, чтобы создать объект [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-). Используйте объект клиента, чтобы получить объект [клиента базы знаний](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest).


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest).
* Для **файлов** используйте объект [Класс FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest).
* Для **URL-адресов** используйте список строк.

Вызовите метод ["Создать"](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) со сведениями о базе знаний. Сведения о базе знаний по сути представляют собой объект JSON.

После возвращения метода "Создать" передайте возвращенный идентификатор операции методу [wait_for_operation](#get-status-of-an-operation) для опроса состояния. Метод "wait_for_operation" возвращает значение после завершения операции. Проанализируйте значение заголовка `resourceLocation` возвращенной операции, чтобы получить новый идентификатор базы знаний.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Чтобы создать базу знаний, добавьте функцию [`wait_for_operation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Базу знаний можно обновить, передав идентификатор базы знаний и метод [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest), содержащий [добавить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [обновить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) и [удалить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) объекты DTO в метод [обновить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-). DTO также являются объектами JSON. Используйте метод [wait_for_operation](#get-status-of-an-operation), чтобы определить, успешно ли выполнено обновление.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Чтобы обновить базу знаний, добавьте функцию [`wait_for_operation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [опубликовать](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется ее в конечной точке. Проверьте код ответа HTTP, чтобы убедиться, что публикация прошла удачно.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Получение опубликованной конечной точки

После публикации базы знаний доступ к опубликованной базе знаний осуществляется через API generateAnswer среды выполнения прогнозирующих запросов. Для этого требуется ключ конечной точки среды выполнения. Он отличается от ключа разработки.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Из вызова возвращаются два ключа конечной точки. Для доступа к конечной точке среды выполнения необходим только один из них.

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний с помощью метода [удалить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) с параметром идентификатора базы знаний.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest). Используйте [свойство Operation.OperationId](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода.

Вызов _delayTimer_ в следующем блоке кода используется для имитации логики повторных попыток. Замените его на собственную логику повторных попыток.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с командой `node index.js` из каталога приложения.

Все фрагменты кода из этой статьи [доступны для скачивания](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) и могут выполняться как один файл.

```console
node index.js
```

Программа выводит состояние на консоль:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)