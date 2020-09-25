---
title: Использование функции Azure как обработчика событий Сетки событий Azure
description: Узнайте, как можно использовать функции Azure в качестве обработчиков событий Сетки событий Azure.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 87aeb78729dcc7bec9f193fab389e5c0952e63d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270336"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Использование функции Azure как обработчика событий Сетки событий

Обработчик событий — это место, куда отправляются события. Обработчик выполняет действие для обработки события. Некоторые службы Azure автоматически настроены для обработки событий. Одна из них — это **Функции Azure**. 

Используйте **Функции Azure** в бессерверной архитектуре для реагирования на события из Сетки событий. При использовании Функций Azure в качестве обработчика используйте триггер Сетки событий вместо универсальных триггеров HTTP. Сетка событий автоматически проверяет триггеры Сетки событий. При использовании универсальных триггеров HTTP вам нужно самостоятельно реализовать [ответ проверки](webhook-event-delivery.md).

См. сведения в статье [Триггер Сетки событий для Функций Azure](../azure-functions/functions-bindings-event-grid.md).

## <a name="tutorials"></a>Учебники

|Title  |Описание  |
|---------|---------|
| [Краткое руководство. Обработка событий с помощью функции](custom-event-to-function.md) | Отправка пользовательского события в функцию для обработки. |
| [Руководство. Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](resize-images-on-storage-blob-upload-event.md) | Пользователи отправляют изображения в учетную запись хранения через веб-приложение. При создании большого двоичного объекта в хранилище служба "Сетка событий" отправляет событие приложению-функции, которое изменяет размер отправленного изображения. |
| [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md) | Когда Центры событий создают файл сбора, служба "Сетка событий" отправляет событие приложению-функции. Это приложение извлекает файл сбора и переносит данные в хранилище данных. |
| [Руководство. с примерами интеграции служебной шины Azure со службой "Сетка событий Azure"](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Сетка событий отправляет сообщения из раздела Служебной шины в приложение-функцию и приложение логики. |

## <a name="rest-example-for-put"></a>Пример REST (для PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Включить пакетную обработку
Для более высокой пропускной способности включите пакетную обработку в подписке. При использовании портал Azure можно задать максимальное количество событий в пакете и предпочтительный размер пакета в килобайтах байт во время создания подписки или после ее создания. 

Параметры пакетной службы можно настроить с помощью шаблона портал Azure, PowerShell, CLI или диспетчер ресурсов. 

### <a name="azure-portal"></a>Портал Azure
Когда вы создаете подписку в пользовательском интерфейсе, на странице **Создание подписки на события** перейдите на вкладку **Дополнительные функции** и задайте для параметра **Максимальное число событий в пакете** и **предпочтительный размер пакета в килобайтах**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Включить пакетную обработку во время создания подписки":::

Вы можете обновить эти значения для существующей подписки на вкладке " **функции** " страницы **раздела "Сетка событий** ". 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Включить пакетную обработку после создания":::

### <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager
**Максевентспербатч** и **преферредбатчсизеинкилобитес** можно задать в шаблоне Azure Resource Manager. Дополнительные сведения см. в [справочнике по шаблонам Microsoft. EventGrid eventSubscriptions](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI
Для настройки параметров, связанных с пакетной службой, можно использовать команду [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) или [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) . для этого используйте следующие параметры: `--max-events-per-batch` или `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
С помощью командлета [New-азевентгридсубскриптион](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) или [Update-азевентгридсубскриптион](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) можно настроить параметры, связанные с пакетной службой, используя следующие параметры: `-MaxEventsPerBatch` или `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> Доставка событий в функцию Azure в **другом клиенте** не поддерживается. 

## <a name="next-steps"></a>Дальнейшие действия
См. список поддерживаемых обработчиков событий в статье [Обработчики событий](event-handlers.md). 
