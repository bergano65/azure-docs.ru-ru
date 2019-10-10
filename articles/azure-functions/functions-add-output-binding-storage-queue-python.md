---
title: Добавление привязки очереди службы хранилища Azure к функции Python
description: Сведения о добавлении выходной привязки очереди службы хранилища Azure к функции Python с помощью Azure CLI и Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839181"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Добавление привязки очереди службы хранилища Azure к функции Python

Функции Azure позволяют выполнять подключение служб Azure и других ресурсов к функциям без необходимости написания кода для интеграции. Эти *привязки*, которые представляют как входные, так и выходные данные, объявляются в определении функции. Данные привязок предоставляются функции в качестве параметров. *Триггер* является специальным типом входных привязок. Хотя функция обладает только одним триггером, она может состоять из нескольких входных и выходных привязок. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

В этой статье приведены сведения об интеграции функции, которая была создана в [предыдущей статье](functions-create-first-function-python.md), с помощью очереди службы хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает в сообщение очереди данные HTTP-запроса.

Большинство привязок требуют сохраненную строку подключения, которая будет использоваться Функциями Azure для доступа к привязанным службам. Чтобы упростить это подключение, вам следует использовать учетную запись хранения, созданную в приложении-функции. Подключение к этой учетной записи уже хранится в параметрах приложения под названием `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этой статьей следует завершить все шаги из статьи [Создание функции, активируемой HTTP, в Azure](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Загрузка параметров приложения-функции

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Включение пакетов расширений

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Теперь вы можете добавить выходную привязку службы хранилища в проект.

## <a name="add-an-output-binding"></a>Добавление выходной привязки

В службе "Функции" для каждого типа привязок требуется `direction`, `type` и уникальное `name`, которое определяется в файле function.json. Способ определения этих атрибутов зависит от языка приложения-функции.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Добавление кода, который использует выходную привязку

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

После использования выходной привязки вам для проверки подлинности, получения ссылки на очередь или записи данных больше не потребуется код пакета SDK службы хранилища Azure. Вместо вас эти задачи будут выполнены выходной привязкой очереди и средой выполнения функции.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Как и прежде, чтобы локально запустить среду выполнения функции, используйте следующую команду.

```bash
func host start
```

> [!NOTE]  
> Из-за того что в предыдущем кратком руководстве в файле host.json вы включили пакеты расширений, во время запуска для вас были загружены и установлены [расширения привязки службы хранилища](functions-bindings-storage-blob.md#packages---functions-2x) вместе с расширениями привязок Майкрософт.

Скопируйте URL-адрес функции `HttpTrigger` из выходных данных среды выполнения и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. В веб-браузере должен появиться ответ, который соответствует ответу из предыдущей статьи.

На этот раз в хранилище очередей выходная привязка также создает очередь с именем `outqueue` и с помощью этой же строки добавляет сообщение.

Затем Azure CLI следует использовать для просмотра новой очереди и проверки добавленного сообщения. Кроме того, очередь можно просматривать с помощью [Обозревателя службы хранилища Microsoft Azure][Azure Storage Explorer] или на [портале Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Установка подключения к учетной записи хранилища

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Запрос в очередь хранилища

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

Теперь пора выполнить повторную публикацию обновленного приложения-функции в Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Чтобы проверить развернутую функцию, вы можете снова использовать cURL в веб-браузере. Как и раньше, добавьте строку запроса `&name=<yourname>` к URL-адресу, как показано в следующем примере.

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Чтобы убедиться, что выходная привязка снова создает новое сообщение в очереди, вы можете [проверить сообщение очереди службы хранилища](#query-the-storage-queue).

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы обновили функцию, активируемую HTTP, которую теперь можно использовать для записи данных в очередь службы хранилища. Чтобы получить дополнительные сведения о разработке Функций Azure с помощью Python, обратитесь к статьям [Справочник разработчика Python. Функции Azure](functions-reference-python.md) и [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md). Полные примеры проектов Функций в Python см. в [этой статье](/samples/browse/?products=azure-functions&languages=python). 

Затем для мониторинга приложения-функции следует включить Application Insights.

> [!div class="nextstepaction"]
> [Включение интеграции с Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/