---
title: Добавление привязки очереди службы хранилища Azure к функции Python
description: Узнайте, как добавлять выходные привязки очереди службы хранилища Azure к функции Python.
ms.date: 10/02/2019
ms.topic: quickstart
ms.openlocfilehash: f2db0f470c4205919343e3838a4f73b05bf71fb0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928866"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Добавление привязки очереди службы хранилища Azure к функции Python

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

В этой статье приведены сведения об интеграции функции, которая была создана в [предыдущей статье](functions-create-first-function-python.md), с помощью очереди службы хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает в сообщение очереди данные HTTP-запроса.

Большинство привязок требуют сохраненную строку подключения, которая будет использоваться Функциями Azure для доступа к привязанным службам. Чтобы упростить это подключение, вам следует использовать учетную запись хранения, созданную в приложении-функции. Подключение к этой учетной записи уже хранится в параметрах приложения под названием `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этой статьей следует завершить все шаги из статьи [Создание функции, активируемой HTTP, в Azure](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Загрузка параметров приложения-функции

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

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
> Так как пакеты расширений включены в файл host.json, во время запуска вместе с другими расширениями привязки Майкрософт было также загружено и установлено [расширение привязки службы хранилища](functions-bindings-storage-blob.md#packages---functions-2x-and-higher).

Скопируйте URL-адрес функции `HttpTrigger` из выходных данных среды выполнения и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. В веб-браузере должен появиться ответ, который соответствует ответу из предыдущей статьи.

На этот раз в хранилище очередей выходная привязка также создает очередь с именем `outqueue` и с помощью этой же строки добавляет сообщение.

Затем Azure CLI следует использовать для просмотра новой очереди и проверки добавленного сообщения. Кроме того, очередь можно просматривать с помощью [Обозревателя службы хранилища Microsoft Azure][Azure Storage Explorer] или на [портале Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Повторное развертывание проекта 

Чтобы обновить опубликованное приложение, используйте команду [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core Tools для развертывания кода проекта в Azure. В этом примере следует заменить `<APP_NAME>` именем приложения.

```command
func azure functionapp publish <APP_NAME> --build remote
```

Чтобы проверить развернутую функцию, вы можете снова использовать cURL в веб-браузере. Как и раньше, добавьте строку запроса `&name=<yourname>` к URL-адресу, как показано в следующем примере.

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Чтобы убедиться, что выходная привязка снова создает новое сообщение в очереди, как и ожидалось, можно [проверить сообщение очереди службы хранилища](#query-the-storage-queue).

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы обновили функцию, активируемую HTTP, которую теперь можно использовать для записи данных в очередь службы хранилища. Чтобы получить дополнительные сведения о разработке Функций Azure с помощью Python, обратитесь к статьям [Справочник разработчика Python. Функции Azure](functions-reference-python.md) и [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md). Полные примеры проектов Функций в Python см. в [этой статье](/samples/browse/?products=azure-functions&languages=python). Дополнительные сведения о ценообразовании см. на [странице цен на Функции](https://azure.microsoft.com/pricing/details/functions/) и в статье [Estimating Consumption plan costs](functions-consumption-costs.md) (Оценка стоимости плана потребления).

Затем для мониторинга приложения-функции следует включить Application Insights.

> [!div class="nextstepaction"]
> [Включение интеграции с Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/