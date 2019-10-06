---
title: Получение данных об изменении ресурса
description: Узнайте, как определить, когда ресурс был изменен, и получить список измененных свойств.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980295"
---
# <a name="get-resource-changes"></a>Получение данных об изменении ресурса

Ресурсы изменяются с помощью ежедневного использования, перенастройки и даже повторного развертывания.
Изменение может быть взято из отдельного пользователя или путем автоматического процесса. Большая часть изменений заключается в проектировании, но иногда это не так. Благодаря журналу изменений за последние 14 дней, граф ресурсов Azure позволяет выполнять следующие задачи:

- просмотр времени обнаружения изменений для свойства Azure Resource Manager;
- просмотр свойств, которые были изменены в ходе этого события изменения.

Обнаружение изменений и сведения полезны в следующих примерах сценариев:

- Во время управления инцидентами, чтобы понять _потенциально_ связанные изменения. Запрос событий изменения в течение определенного периода времени и оценка сведений об изменении.
- Поддержание базы данных управления конфигурацией, известной как CMDB, в актуальном состоянии. Вместо обновления всех ресурсов и их полных наборов свойств по расписанию, изменяйте только то, что изменилось.
- Понимание того, какие другие свойства могли быть изменены, когда ресурс изменил состояние соответствия. Оценка этих дополнительных свойств может предоставить сведения о других свойствах, которые могут потребоваться управлять с помощью определения политики Azure.

В этой статье показано, как собирать эти сведения с помощью пакета SDK для Graph. Чтобы просмотреть эти сведения в портал Azure, см. статью журнал [изменений](../../policy/how-to/determine-non-compliance.md#change-history-preview) политики Azure или [Журнал изменений](../../../azure-monitor/platform/activity-log-view.md#azure-portal)журнала действий Azure.

> [!NOTE]
> Сведения об изменениях в диаграмме ресурсов предназначены для диспетчер ресурсов свойств. Сведения об отслеживании изменений в виртуальной машине см. в статье об [отслеживании](../../../automation/automation-change-tracking.md) изменений в службе автоматизации Azure или [гостевой конфигурации политики Azure для виртуальных машин](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Журнал изменений в графе ресурсов Azure доступен в общедоступной предварительной версии.

## <a name="find-when-changes-were-detected"></a>Найти, когда были обнаружены изменения

Первым шагом в просмотре изменений в ресурсе является поиск событий изменения, связанных с этим ресурсом, в течение определенного периода времени. Этот шаг выполняется через конечную точку **ресаурцечанжес** RESTful.

Конечная точка **ресаурцечанжес** должна иметь два параметра в тексте запроса:

- **resourceId**: Ресурс Azure для поиска изменений.
- **интервал**: Свойство с датами _начала_ и _окончания_ для проверки события изменения с помощью часового **пояса Zulu (Z)** .

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

В приведенном выше тексте запроса REST API универсальный код ресурса (URI) для **ресаурцечанжес** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Ответ выглядит примерно так:

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

Каждое обнаруженное событие изменения для **resourceId** имеет **changeId** , уникальный для этого ресурса. Хотя строка **changeId** иногда может содержать другие свойства, гарантируется только ее уникальность. Запись изменений включает время создания моментальных снимков до и после.
Событие Change произошло в некоторый момент времени в этом окне.

## <a name="see-what-properties-changed"></a>Просмотр измененных свойств

С помощью **changeId** из конечной точки **ресаурцечанжес** конечная точка **ресаурцечанжедетаилс** RESTful используется для получения конкретных особенностей события изменения.

Конечная точка **ресаурцечанжедетаилс** должна иметь два параметра в тексте запроса:

- **resourceId**: Ресурс Azure для поиска изменений.
- **changeId**: Уникальное событие изменения для **resourceId** , собранное из **ресаурцечанжес**.

Пример тела запроса:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

В приведенном выше тексте запроса REST API универсальный код ресурса (URI) для **ресаурцечанжедетаилс** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Ответ выглядит примерно так:

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

**бефореснапшот** и **афтерснапшот** предоставляют время создания моментального снимка и свойства в это время. Это изменение произошло в некоторой точке между этими снимками. Взглянув на приведенный выше пример, можно увидеть, что измененное свойство было **supportsHttpsTrafficOnly**.

Чтобы сравнить результаты программным способом, Сравните часть **содержимого** каждого моментального снимка, чтобы определить разницу. При сравнении всего моментального снимка **Метка времени** всегда отображается как разница, несмотря на ожидаемую.

## <a name="next-steps"></a>Следующие шаги

- См. язык, используемый в [начальных запросах](../samples/starter.md).
- См. Дополнительные сведения о расширенном использовании в [расширенных запросах](../samples/advanced.md).
- Узнайте, как [исследовать ресурсы](../concepts/explore-resources.md).