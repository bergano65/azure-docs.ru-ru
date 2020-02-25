---
title: Публикации устойчивых функций в службе "Сетка событий Azure" (предварительная версия)
description: Узнайте, как настроить автоматическую публикацию в службе "Сетка событий Azure" для устойчивых функций.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ee60dadc90af5a9b941ba890bddb9b96de3f35d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562176"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Публикации устойчивых функций в службе "Сетка событий Azure" (предварительная версия)

В этой статье показано, как настроить Устойчивые функции для публикации событий жизненного цикла оркестрации (например, события создания, завершения и сбоя) в пользовательском [разделе Сетки событий Azure](https://docs.microsoft.com/azure/event-grid/overview).

Ниже приведены некоторые сценарии, где эта функция полезна:

* **DevOps такие сценарии, как развертывание со синим/зеленым**цветом: перед реализацией [стратегии параллельного развертывания](durable-functions-versioning.md#side-by-side-deployments)может потребоваться выяснить, выполняются ли какие-либо задачи.

* **Расширенная поддержка мониторинга и диагностики**. Вы можете отслеживать сведения о состоянии оркестрации во внешнем хранилище, оптимизированном для запросов, таких как база данных SQL Azure или Azure Cosmos DB.

* **Продолжительное фоновое действие**. При использовании устойчивых функций для продолжительного фонового действия эта функция позволяет вам узнать текущее состояние.

## <a name="prerequisites"></a>Предварительные требования

* Установите [Microsoft. Azure. веб-jobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) в проекте устойчивые функции.
* Установите [эмулятор хранения Azure](../../storage/common/storage-use-emulator.md) (только для Windows) или используйте существующую учетную запись хранения Azure.
* Установите [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-a-custom-event-grid-topic"></a>Создание пользовательского раздела сетки событий

Создайте раздел сетки событий для отправки событий из устойчивых функций. Ниже показано, как создать раздел с помощью Azure CLI. Это также можно сделать с [помощью PowerShell](../../event-grid/custom-event-quickstart-powershell.md) или [портал Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды `az group create`. Сейчас служба "Сетка событий Azure" не поддерживает все регионы. Сведения о поддерживаемых регионах см. в статье [Общие сведения о службе "Сетка событий Azure](../../event-grid/overview.md)".

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Создание пользовательской темы

Раздел сетки событий содержит определяемую пользователем конечную точку, в которой можно размещать свое событие. Замените `<topic_name>` уникальным именем для вашей темы. Имя раздела должно быть уникальным, так как оно становится записью службы доменных имен (DNS).

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Получение конечной точки и ключа

Получите конечную точку раздела. Замените `<topic_name>` на выбранное имя.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Получите ключ раздела. Замените `<topic_name>` на выбранное имя.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Теперь можно отправлять события в раздел.

## <a name="configure-event-grid-publishing"></a>Настройка публикации сетки событий

Найдите файл `host.json` в вашем проекте устойчивых функций.

Добавьте `eventGridTopicEndpoint` и `eventGridKeySettingName` в свойство `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Возможные свойства конфигурации службы "Сетка событий Azure" можно найти в [документации Host. JSON](../functions-host-json.md#durabletask). После настройки файла `host.json` приложение функции отправляет события жизненного цикла в сетку событий. Это работает при запуске приложения функции как локально, так и в Azure.

Установите параметр приложения для ключа раздела в приложении-функции и `local.settings.json`. Следующий код JSON является примером `local.settings.json` для локальной отладки. Замените `<topic_key>` на ключ раздела.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Если используется [эмулятор хранения](../../storage/common/storage-use-emulator.md) (только для Windows), убедитесь, что он работает. Рекомендуется запустить команду `AzureStorageEmulator.exe clear all` перед выполнением.

Если вы используете существующую учетную запись хранения Azure, замените `UseDevelopmentStorage=true` в `local.settings.json` со своей строкой подключения.

## <a name="create-functions-that-listen-for-events"></a>Создание функций, которые прослушивают события

С помощью портал Azure создайте другое приложение функции для прослушивания событий, опубликованных вашим приложением Устойчивые функции. Его лучше размещать в том же регионе, что и сетка событий.

### <a name="create-an-event-grid-trigger-function"></a>Создание функции триггера сетки событий

Создайте функцию для получения событий жизненного цикла. Выберите **Пользовательская функция**.

![Выберите создание пользовательской функции.](./media/durable-functions-event-publishing/functions-portal.png)

Выберите триггер сетки событий и выберите язык.

![Выберите триггер сетки событий.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Введите имя функции, а затем выберите `Create`.

![Создайте триггер сетки событий.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Будет создана функция со следующим кодом:

# <a name="c-script"></a>[C#Индекса](#tab/csharp-script)

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Выберите `Add Event Grid Subscription`. Эта операция добавляет подписку службы "Сетка событий" для созданного вами раздела. Дополнительные сведения см. в разделе [Основные понятия в службе "Сетка событий Azure"](https://docs.microsoft.com/azure/event-grid/concepts).

![Выберите ссылку на триггер сетки событий.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Выберите `Event Grid Topics` в качестве **типа раздела**. Выберите для раздела сетки событий созданную вами группу ресурсов. Затем выберите экземпляр раздела сетки событий. Нажмите кнопку `Create`.

![создание подписки в службе "Сетка событий";](./media/durable-functions-event-publishing/eventsubscription.png)

Теперь все готово для получения событий жизненного цикла.

## <a name="run-durable-functions-app-to-send-the-events"></a>Запуск Устойчивые функции приложения для отправки событий

В проекте Устойчивые функции, который вы настроили ранее, запустите отладку на локальном компьютере и начните согласование. Приложение публикует события жизненного цикла Устойчивые функции в сетке событий. Убедитесь, что функция "Сетка событий" запускает созданную функцию прослушивателя, проверив ее журналы в портал Azure.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Схема событий

В приведенном ниже списке описана схема событий жизненного цикла:

* **`id`** : уникальный идентификатор для события сетки событий.
* **`subject`** : путь к теме события. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` будет `Running`, `Completed`, `Failed` и `Terminated`.  
* **`data`** : устойчивые функции определенные параметры.
  * **`hubName`** : [таскхуб](durable-functions-task-hubs.md) имя.
  * **`functionName`** : имя функции Orchestrator.
  * **`instanceId`** : устойчивые функции InstanceId.
  * **`reason`** : дополнительные данные, связанные с событием отслеживания. Дополнительные сведения см. в статье [Диагностика в устойчивых функциях (Функции Azure)](durable-functions-diagnostics.md).
  * **`runtimeStatus`** : состояние среды выполнения оркестрации. "Running", "Completed", "Failed", "Canceled".
* **`eventType`** : "орчестраторевент"
* **`eventTime`** : время события (UTC).
* **`dataVersion`** : версия схемы событий жизненного цикла.
* **`metadataVersion`** : версия метаданных.
* **`topic`** : ресурс раздела сетки событий.

## <a name="how-to-test-locally"></a>Локальное тестирование

Для локального тестирования прочтите [таблицу событий функции Azure локальная отладка](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Управление экземплярами в устойчивых функциях (Функции Azure)](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Управление версиями в устойчивых функциях (Функции Azure)](durable-functions-versioning.md)
