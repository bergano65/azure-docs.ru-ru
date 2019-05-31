---
title: Получение данных об изменении ресурса
description: Понять, как найти изменения ресурса и получить список свойств, которые изменены.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: b6ef57a3f39c82be30d92aef72c1bbe03b653768
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236512"
---
# <a name="get-resource-changes"></a>Получение данных об изменении ресурса

Ресурсы получить изменены на протяжении всей ежедневного перенастройки и даже повторного развертывания.
Изменения могут поступать от отдельного лица или автоматическим процессом. Большинство изменений сделано намеренно, но иногда это не так. За последние 14 дней журнал изменений график ресурсов Azure позволяют:

- просмотр времени обнаружения изменений для свойства Azure Resource Manager;
- просмотр свойств, которые были изменены в ходе этого события изменения.

Обнаружение изменений и сведения можно использовать для следующих примеров сценариев:

- Во время управления инцидентами, чтобы понять _потенциально_ связанные с ними изменения. Запрос для событий изменения во время определенного периода времени и оценить измените данные.
- Сохранение базы данных управления конфигурацией, известный как CMDB актуальном состоянии. Вместо обновления все ресурсы и их полное свойство наборы с заданной частотой, получить только что изменилось.
- Основные сведения о какие-либо другие свойства могут измениться после ресурса изменилось состояние соответствия. Оценка этих дополнительных свойств можно получить представление о других свойств, которым необходимо управлять с помощью определения политики Azure.

В этой статье показано, как для сбора этой информации с помощью пакета SDK график ресурсов. Чтобы просмотреть эти сведения на портале Azure, см. в разделе Политика Azure [журнал изменений](../../policy/how-to/determine-non-compliance.md#change-history-preview) или журнал действий Azure [журнал изменений](../../../azure-monitor/platform/activity-log-view.md#azure-portal).

> [!NOTE]
> Измените данные в график ресурсов предназначены для свойств Resource Manager. Для отслеживания изменений на виртуальной машине, см. в разделе службы автоматизации Azure [отслеживание изменений](../../../automation/automation-change-tracking.md) или политики Azure [конфигурации гостевых виртуальных машин](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Журнал изменений в график ресурсов Azure находится в общедоступной предварительной версии.

## <a name="find-when-changes-were-detected"></a>Найти, если обнаружены изменения

Видите, что изменилось в ресурсе первым делом для поиска событий изменения, относящиеся к этому ресурсу в пределах периода времени. Этот шаг выполняется с помощью **resourceChanges** конечной точки REST.

**ResourceChanges** конечной точки требуется два параметра в тексте запроса:

- **resourceId**: Просмотрите изменения на ресурс Azure.
- **Интервал**: Свойство с _запустить_ и _окончания_ даты его проверки для событий изменения с помощью **Zulu часового пояса (Z)** .

Пример тела запроса:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

В приведенном выше текст запроса, URI API REST для **resourceChanges** является:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Ответ выглядит как в этом примере:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Каждое обнаруженное событие изменения для **resourceId** имеет **changeId** , является уникальным для этого ресурса. Хотя **changeId** строка иногда могут содержать другие свойства, гарантируется только должны быть уникальными. Запись изменений включает время, до и после моментальные снимки.
Изменить событие, произошедшее в определенный момент в этом окне времени.

## <a name="see-what-properties-changed"></a>Увидеть измененные свойства

С помощью **changeId** из **resourceChanges** конечной точки, **resourceChangeDetails** конечной точки REST затем используется для получения события изменения.

**ResourceChangeDetails** конечной точки требуется два параметра в тексте запроса:

- **resourceId**: Просмотрите изменения на ресурс Azure.
- **значение changeId**: События изменения, уникальные для **resourceId** собранные из **resourceChanges**.

Пример тела запроса:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

В приведенном выше текст запроса, URI API REST для **resourceChangeDetails** является:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Ответ выглядит как в этом примере:

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

**beforeSnapshot** и **afterSnapshot** каждого укажите время создания моментального снимка и свойства в это время. На определенном этапе между этих моментальных снимков произошло изменение. Взглянув на приведенном выше примере, мы видим, что измененное свойство было **supportsHttpsTrafficOnly**.

Для сравнения результатов программными средствами, сравните **содержимого** часть каждого моментального снимка, чтобы определить разницу. Если сравнить всего моментального снимка, **timestamp** всегда будет отображаться как различие, несмотря на то ожидалось.

## <a name="next-steps"></a>Дальнейшие действия

- См. в разделе язык используется в [запросы Starter](../samples/starter.md).
- См. Дополнительные использует в [расширенных запросов](../samples/advanced.md).
- Узнайте, как [ознакомьтесь с ресурсами](../concepts/explore-resources.md).