---
title: Публикации устойчивых функций в службе "Сетка событий Azure" (предварительная версия)
description: Узнайте, как настроить автоматическую публикацию в службе "Сетка событий Azure" для устойчивых функций.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249758"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Публикации устойчивых функций в службе "Сетка событий Azure" (предварительная версия)

В этой статье показано, как настроить Устойчивые функции для публикации событий жизненного цикла оркестрации (например, события создания, завершения и сбоя) в пользовательском [разделе Сетки событий Azure](https://docs.microsoft.com/azure/event-grid/overview).

Ниже приведены некоторые сценарии, где эта функция полезна:

* **Сценарии DevOps, такие как синие/зеленые развертывания:** Вы можете знать, выполняются ли какие-либо задачи перед реализацией [стратегии развертывания бок о бок.](durable-functions-versioning.md#side-by-side-deployments)

* **Расширенная поддержка мониторинга и диагностики:** Вы можете отслеживать информацию о состоянии оркестровки во внешнем хранилище, оптимизированном для запросов, таких как база данных Azure S'L или Azure Cosmos DB.

* **Продолжительное фоновое действие**. При использовании устойчивых функций для продолжительного фонового действия эта функция позволяет вам узнать текущее состояние.

## <a name="prerequisites"></a>Предварительные требования

* Установите [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) в проекте «Долгосрочные функции».
* Установите [эмулятор хранения azure](../../storage/common/storage-use-emulator.md) (только Windows) или используйте существующую учетную запись хранения Azure.
* Установите [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-a-custom-event-grid-topic"></a>Создание пользовательского раздела сетки событий

Создайте раздел сетки событий для отправки событий из устойчивых функций. Ниже показано, как создать раздел с помощью Azure CLI. Вы также можете сделать это [с помощью PowerShell](../../event-grid/custom-event-quickstart-powershell.md) или [с помощью портала Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды `az group create`. В настоящее время Azure Event Grid поддерживает не все регионы. Для получения информации о том, какие регионы поддерживаются, см. [Azure Event Grid overview](../../event-grid/overview.md)

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Создание пользовательской темы

Раздел сетки событий содержит определяемую пользователем конечную точку, в которой можно размещать свое событие. Замените `<topic_name>` уникальным именем для вашей темы. Имя раздела должно быть уникальным, так как оно становится записью службы доменных имен (DNS).

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Получение конечной точки и ключа

Получите конечную точку раздела. Замените `<topic_name>` на выбранное имя.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Получите ключ раздела. Замените `<topic_name>` на выбранное имя.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Теперь можно отправлять события в раздел.

## <a name="configure-event-grid-publishing"></a>Настройка публикации Event Grid

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

Возможные свойства конфигурации Azure Event Grid можно найти в [документации host.json.](../functions-host-json.md#durabletask) После настройки `host.json` файла приложение функции отправляет события жизненного цикла в тему Event Grid. Это работает при запуске приложения функции как локально, так и в Azure.

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

Если вы используете [эмулятор хранения данных](../../storage/common/storage-use-emulator.md) (только Windows), убедитесь, что он работает. Рекомендуется запустить команду `AzureStorageEmulator.exe clear all` перед выполнением.

Если вы используете существующую учетную `UseDevelopmentStorage=true` `local.settings.json` запись хранения Azure, замените ее строку подключения.

## <a name="create-functions-that-listen-for-events"></a>Создание функций, которые прослушивают события

Используя портал Azure, создайте другое функциональное приложение для прослушивания событий, опубликованных вашим приложением Durable Functions. Лучше всего найти его в том же регионе, что и тема Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Создание функции триггера сетки событий

Создайте функцию для получения событий жизненного цикла. Выберите **Пользовательская функция**.

![Выберите создание пользовательской функции.](./media/durable-functions-event-publishing/functions-portal.png)

Выберите Event Grid Trigger и выберите язык.

![Выберите триггер сетки событий.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Введите имя функции, а затем выберите `Create`.

![Создайте триггер сетки событий.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Будет создана функция со следующим кодом:

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

## <a name="run-durable-functions-app-to-send-the-events"></a>Выполнить приложение «Долгосрочные функции» для отправки событий

В проекте «Долгосрочные функции», который вы настроили ранее, начните отладку локальной машины и начните оркестровку. Приложение публикует события жизненного цикла долгосрочных функций в Event Grid. Проверить, что Event Grid запускает созданную функцию слушателя, проверяя его журналы на портале Azure.

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

* **`id`**: Уникальный идентификатор для события Event Grid.
* **`subject`**: Путь к теме события. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` будет `Running`, `Completed`, `Failed` и `Terminated`.  
* **`data`**: Долгосрочные функции специфические параметры.
  * **`hubName`**: Имя [TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: Название функции оркестратора.
  * **`instanceId`**: Долгосрочные функции instanceId.
  * **`reason`**: Дополнительные данные, связанные с событием отслеживания. Дополнительные сведения см. в статье [Диагностика в устойчивых функциях (Функции Azure)](durable-functions-diagnostics.md).
  * **`runtimeStatus`**: Оркестрирование Статуса Runtime. "Running", "Completed", "Failed", "Canceled".
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Время событий (UTC).
* **`dataVersion`**: Версия схемы события жизненного цикла.
* **`metadataVersion`**: Версия метаданных.
* **`topic`**: Ресурс темы сетки событий.

## <a name="how-to-test-locally"></a>Локальное тестирование

Чтобы протестировать локально, прочитайте [локальный отладку Azure Function Event Grid Trigger.](../functions-debug-event-grid-trigger-local.md)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Управление экземплярами в устойчивых функциях (Функции Azure)](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Управление версиями в устойчивых функциях (Функции Azure)](durable-functions-versioning.md)
