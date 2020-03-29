---
title: Получение данных об изменении ресурса
description: Понять, как найти, когда ресурс был изменен, получить список свойств, которые изменились, и оценить диффы.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873035"
---
# <a name="get-resource-changes"></a>Получение данных об изменении ресурса

Ресурсы изменяются в ходе ежедневного использования, перенастройки и даже передислокации.
Изменения могут исходить от человека или автоматизированного процесса. Большинство изменений по дизайну, но иногда это не так. За последние 14 дней истории изменений график ресурсов Azure позволяет:

- Найти, когда были обнаружены изменения в свойстве управления ресурсами Azure
- Для каждого изменения ресурса см. сведения об изменении свойств
- Посмотреть полное сравнение ресурса до и после обнаруженного изменения

Обнаружение изменений и детали являются ценными для следующих сценариев примера:

- Во время управления инцидентами, чтобы понять _потенциально_ связанные изменения. Запрос на изменение событий в течение определенного времени и оценка деталей изменения.
- Обновление базы данных управления конфигурацией, известной как CMDB. Вместо того, чтобы обновлять все ресурсы и наборы их полного свойства на запланированной частоте, только получить то, что изменилось.
- Понимание того, какие другие свойства могли быть изменены при изменении состояния соответствия ресурсу. Оценка этих дополнительных свойств может дать представление о других свойствах, которые, возможно, потребуется управлять с помощью определения политики Azure.

В этой статье показано, как собрать эту информацию через SDK Resource Graph. Ознакомиться с этой информацией можно на портале [Change history](../../policy/how-to/determine-non-compliance.md#change-history-preview) Azure, см. [Change history](../../../azure-monitor/platform/activity-log-view.md#azure-portal)
Подробнее об изменениях в приложениях из слоя инфраструктуры вплоть до развертывания приложений [можно](../../../azure-monitor/app/change-analysis.md) узнать в Azure Monitor.

> [!NOTE]
> Детали изменения в графике ресурсов относятся к свойствам менеджера ресурсов. Для отслеживания изменений внутри виртуальной машины [Change tracking](../../../automation/automation-change-tracking.md) см. [Guest Configuration for VMs](../../policy/concepts/guest-configuration.md)

> [!IMPORTANT]
> История изменений в графике ресурсов Azure находится в общедоступном предварительном просмотре.

## <a name="find-detected-change-events-and-view-change-details"></a>Поиск обнаруженных событий изменения и сведения об изменениях представления

Первым шагом в поиске изменений на ресурсе является поиск событий изменения, связанных с этим ресурсом, в течение определенного времени. Каждое событие изменения также содержит подробную информацию о том, что изменилось на ресурсе. Этот шаг выполняется через **ресурсИзменения** в конечную точку REST.

Конечная точка **ресурсаИзменения** принимает следующие параметры в органе запроса:

- \] **требуется ресурс resourceId:** \[ресурс Azure для поиска изменений.
- **требуется** \[\]интервал : Свойство с датами _начала_ и _окончания,_ когда необходимо проверить событие изменения с помощью **зоны времени Зулуса ( )**.
- **fetchPropertyChanges** (необязательно): свойство boolean, которое устанавливает, если объект ответа включает изменения свойств.

Пример тела запроса:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

С вышеуказанным органом запроса, REST API URI для **ресурсовИзменения:**

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Ответ похож на этот пример:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Каждое обнаруженное событие изменения для **ресурса Имеет** следующие свойства:

- **changeId** - Это значение является уникальным для этого ресурса. В то время как строка **changeId** иногда может содержать другие свойства, она только гарантированно будет уникальной.
- **передснимком** - Содержит **моментальный снимок** и **отметку времени** снимка ресурса, который был сделан до обнаружения изменения.
- **afterSnapshot** - Содержит **моментальный снимок** и **отметку времени** снимка ресурса, сделанного после обнаружения изменения.
- **changeType** - Описывает тип изменений, обнаруженных для всей записи **изменений** между beforeSnapshot и **afterSnapshot.** Значения: _Создание,_ _обновление_и _удаление_. Массив **свойствИзменяет** свойств только при _обновлении_ **changeType** .
- **propertyChanges** - Этот массив свойств детализирует все свойства ресурсов, которые были обновлены между **beforeSnapshot** и **afterSnapshot:**
  - **propertyName** - Название ресурсного свойства, которое было изменено.
  - **changeCategory** - Описывает, что сделало изменение. Значения: _Система_ и _пользователь_.
  - **changeType** - Описывает тип изменений, обнаруженных для индивидуального свойства ресурса.
    Значения: _Вставьте,_ _Обновление_, _Удалить_.
  - **beforeValue** - Стоимость ресурсного свойства в **beforeSnapshot**. Не отображается при включении _Insert_в меню **changeType.**
  - **afterValue** - Стоимость ресурсного свойства в **afterSnapshot**. Не отображается при **удалении changeType** _._

## <a name="compare-resource-changes"></a>Сравнение изменений ресурсов

При **изменении испуна** с конечной точки **changechange,** конечная точка **resourceChanges** REST затем используется для получения снимков ресурсов до и после.

Конечная точка **ресурсаChangeDetails** требует двух параметров в органе запроса:

- **resourceId**: Ресурс Azure для сравнения изменений.
- **changeId**: Уникальное событие изменения для **ресурсаId,** собранное из **ресурсовChanges**.

Пример тела запроса:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

С вышеуказанным органом запроса, REST API URI для **resourceChangeDetails:**

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Ответ похож на этот пример:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**доСнимок** и **afterSnapshot** каждый дать время снимок был сделан и свойства в то время. Изменение произошло в какой-то момент между этими снимками. Глядя на приведенный выше пример, мы видим, что изменяемое свойство было **supportsHttpsTrafficOnly**.

Для сравнения результатов либо используйте свойство **изменений** в **ресурсеИзменения,** либо оцените **часть содержимого** каждого снимка в **ресурсеChangeDetails,** чтобы определить разницу. Если вы сравните снимки, **метка времени** всегда отображается как разница, несмотря на ожидаемые.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с языком, который используется в [запросах Starter.](../samples/starter.md)
- Просмотрите расширенные запросы в [Расширенных запросах.](../samples/advanced.md)
- Узнайте больше о том, как [изучать ресурсы](../concepts/explore-resources.md).