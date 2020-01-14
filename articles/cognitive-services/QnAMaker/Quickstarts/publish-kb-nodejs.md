---
title: Краткое руководство. QnA Maker с REST API для Node.js
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как начать работу с REST API QnA Maker для Node.js. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 3ca664afc458f794e07782354d0bad5c46c66537
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451092"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Краткое руководство. QnA Maker с REST API для Node.js

Начните работу с QnA Maker с REST API для Node.js. Выполните следующие действия, чтобы опробовать пример кода для базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам.

Используйте QnA Maker с REST API для Node.js для:

* Создание базы знаний
* Замена базы знаний
* Публикация базы знаний
* Удаление базы знаний
* Скачивание базы знаний
* Получение состояния операции

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Образцы Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org).
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и конечную точку (включая имя ресурса), щелкните **Быстрый запуск** для ресурса на портале Azure.

## <a name="setting-up"></a>Настройка

### <a name="create-a-qna-maker-azure-resource"></a>Создание ресурса QnA Maker Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс с помощью [Create a Cognitive Services account using the Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)(создания учетной записи Cognitive Services с помощью портала Azure) или [Create a Cognitive Services account using the Azure Command-Line Interface(CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) (создания учетной записи Cognitive Services с помощью интерфейса командной строки Azure (CLI)) на локальном компьютере.

После получения ключа от своего ресурса [создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ресурса с именами `QNAMAKER_RESOURCE_KEY` и `QNAMAKER_AUTHORING_ENDPOINT`. Используйте значения ключа и конечной точки, найденные на странице **быстрого запуска** ресурса на портале Azure.

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него.

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init -y`, чтобы создать файл узла `package.json`.

```console
npm init -y
```

Добавьте NPM пакеты `reqeuestretry` и `request`:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью QnA Maker с REST API для Node.js:

* [Создание базы знаний](#create-a-knowledge-base)
* [Замена базы знаний](#replace-a-knowledge-base)
* [Публикация базы знаний](#publish-a-knowledge-base)
* [Удаление базы знаний](#delete-a-knowledge-base)
* [Скачивание базы знаний](#download-the-knowledge-base)
* [Получение состояния операции](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Добавление зависимостей

Создайте файл с именем `rest-apis.js` и добавьте следующий оператор _запроса_ для выполнения HTTP-запросов.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Добавление сведений о ресурсах Azure

Создайте переменные для конечной точки Azure и ключа ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

Задайте следующие значения среды:

* `QNAMAKER_RESOURCE_KEY`. **Ключ** — это строка из 32 символов, которая доступна на странице "Быстрый запуск" ресурса QnA Maker на портале Azure. Это не то же самое, что ключ конечной точки прогнозирования.
* `QNAMAKER_AUTHORING_ENDPOINT`. Конечная точка разработки (в формате `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`) содержит **имя ресурса**. Это не тот же URL-адрес, который используется для запроса конечной точки прогнозирования.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

В базе знаний хранятся пары вопросов и ответов, созданные из объекта JSON:

* **Редакционный контент**.
* **Файлы** — локальные файлы, не требующие разрешения.
* **URL** -адреса — общедоступные URL.

Используйте [REST API для создания базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Замена базы знаний

Используйте [REST API для замены базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Публикация базы знаний. Этот процесс делает базу знаний доступной из конечной точки прогнозирования HTTP-запросов.

Используйте [REST API для публикации базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Загрузка базы знаний

Используйте [REST API для загрузки базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

После завершения использования базы знаний удалите ее.

Используйте [REST API для удаления базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Длительные процессы, такие как процесс создания, возвращают идентификатор операции, который необходимо проверить с помощью отдельного вызова REST API. Эта функция принимает текст ответа "Создать". Важным ключом является `operationState`, который определяет, нужно ли продолжать опрос.

Используйте [REST API для отслеживания операций в базе знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с командой `node rest-apis.js` из каталога приложения.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>[Руководство. Создание базы знаний и ответ на вопрос](../tutorials/create-publish-query-in-portal.md)

* [Что такое QnA Maker?](../Overview/overview.md)
* [Редактирование базы знаний](../how-to/edit-knowledge-base.md)
* [Get analytics on your knowledge base](../how-to/get-analytics-knowledge-base.md) (Получение аналитики по своей базе знаний)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).