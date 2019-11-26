---
title: Получение данных об изменении ресурса
description: Узнайте, как определить, когда ресурс был изменен, получить список измененных свойств и оценить различия.
ms.date: 10/09/2019
ms.topic: conceptual
ms.openlocfilehash: 2ed2c0ef8638744aeaa9f31eded14d1716d891ae
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74303992"
---
# <a name="get-resource-changes"></a>Получение данных об изменении ресурса

В ходе ежедневного использования, перенастройки и даже повторного развертывания в ресурсы вносятся изменения.
Изменение может быть взято из отдельного пользователя или путем автоматического процесса. Большая часть изменений заключается в проектировании, но иногда это не так. Благодаря журналу изменений за последние 14 дней, граф ресурсов Azure позволяет выполнять следующие задачи:

- Найти, когда были обнаружены изменения в свойстве Azure Resource Manager
- Сведения об изменении каждого ресурса см. в статье изменение свойства.
- Ознакомьтесь с полным сравнением ресурса до и после обнаруженного изменения

Обнаружение изменений и сведения полезны в следующих примерах сценариев:

- Во время управления инцидентами, чтобы понять _потенциально_ связанные изменения. Запрос событий изменения в течение определенного периода времени и оценка сведений об изменении.
- Поддержание базы данных управления конфигурацией, известной как CMDB, в актуальном состоянии. Вместо обновления всех ресурсов и их полных наборов свойств по расписанию, изменяйте только то, что изменилось.
- Понимание того, какие другие свойства могли быть изменены, когда ресурс изменил состояние соответствия. Оценка этих дополнительных свойств может предоставить сведения о других свойствах, которые могут потребоваться управлять с помощью определения политики Azure.

В этой статье показано, как собирать эти сведения с помощью пакета SDK для Graph. Чтобы просмотреть эти сведения в портал Azure, см. статью журнал [изменений](../../policy/how-to/determine-non-compliance.md#change-history-preview) политики Azure или [Журнал изменений](../../../azure-monitor/platform/activity-log-view.md#azure-portal)журнала действий Azure.
Дополнительные сведения об изменениях в приложениях с уровня инфраструктуры для всех способов развертывания приложений см. в разделе [Использование анализа изменений приложений (Предварительная версия)](../../../azure-monitor/app/change-analysis.md) в Azure Monitor.

> [!NOTE]
> Сведения об изменениях в диаграмме ресурсов предназначены для диспетчер ресурсов свойств. Сведения об отслеживании изменений в виртуальной машине см. в статье об [отслеживании](../../../automation/automation-change-tracking.md) изменений в службе автоматизации Azure или [гостевой конфигурации политики Azure для виртуальных машин](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Журнал изменений в графе ресурсов Azure доступен в общедоступной предварительной версии.

## <a name="find-detected-change-events-and-view-change-details"></a>Найти обнаруженные события изменения и сведения об изменении представления

Первым шагом в просмотре изменений в ресурсе является поиск событий изменения, связанных с этим ресурсом, в течение определенного периода времени. Каждое событие изменения также содержит сведения о том, что изменилось в ресурсе. Этот шаг выполняется через конечную точку **ресаурцечанжес** RESTful.

Конечная точка **ресаурцечанжес** принимает следующие параметры в тексте запроса:

- требуется \[**resourceId**\]: ресурс Azure для поиска изменений.
- **interval** \[требуется\]: свойство с датами _начала_ и _окончания_ для проверки события изменения с помощью часового **пояса Zulu (Z)** .
- **фетчпропертичанжес** (необязательно): логическое свойство, которое задает, включает ли объект ответа изменения свойств.

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

В приведенном выше тексте запроса REST API универсальный код ресурса (URI) для **ресаурцечанжес** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Ответ выглядит примерно так:

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

Каждое обнаруженное событие изменения для **resourceId** имеет следующие свойства.

- **changeId** — это значение уникально для этого ресурса. Хотя строка **changeId** иногда может содержать другие свойства, гарантируется только ее уникальность.
- **бефореснапшот** — содержит **snapshotId** и **метку времени** для моментального снимка ресурса, сделанного до обнаружения изменений.
- **афтерснапшот** — содержит **snapshotId** и **метку времени** для моментального снимка ресурса, сделанного после обнаружения изменений.
- **ChangeType** — описывает тип изменений, обнаруженных для всей записи об изменениях между **бефореснапшот** и **афтерснапшот**. Значения: _Создание_, _Обновление_и _Удаление_. Массив свойств **пропертичанжес** включается только при обновлении **ChangeType** .
- **пропертичанжес** — этот массив свойств содержит сведения обо всех свойствах ресурсов, которые были обновлены между **бефореснапшот** и **афтерснапшот**.
  - **PropertyName** — имя измененного свойства ресурса.
  - **чанжекатегори** — описывает, что делало изменение. Значения: _System_ и _User_.
  - **ChangeType** — описывает тип изменений, обнаруженных для отдельного свойства ресурса.
    Значения: _INSERT_, _Update_, _Remove_.
  - **бефоревалуе** — значение свойства ресурса в **бефореснапшот**. Не отображается при _вставке_ **ChangeType** .
  - **афтервалуе** — значение свойства ресурса в **афтерснапшот**. Не отображается, когда ChangeType _удаляется._

## <a name="compare-resource-changes"></a>Сравнение изменений ресурсов

При использовании **changeId** из конечной точки **ресаурцечанжес** конечная точка **ресаурцечанжедетаилс** RESTful используется для получения моментальных снимков до и после изменения ресурса, который был изменен.

Конечная точка **ресаурцечанжедетаилс** должна иметь два параметра в тексте запроса:

- **resourceId**— ресурс Azure для сравнения изменений.
- **changeId**: уникальное событие изменения для **resourceId** , собранное из **ресаурцечанжес**.

Пример тела запроса:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
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

Чтобы сравнить результаты, либо используйте свойство **Changes** в **ресаурцечанжес** , либо оцените часть **содержимого** каждого моментального снимка в **ресаурцечанжедетаилс** , чтобы определить разницу. При сравнении моментальных снимков **Метка времени** всегда отображается в виде разницы, несмотря на ожидаемую.

## <a name="next-steps"></a>Дополнительная информация

- См. язык, используемый в [начальных запросах](../samples/starter.md).
- См. Дополнительные сведения о расширенном использовании в [расширенных запросах](../samples/advanced.md).
- Узнайте больше о том, как [изучать ресурсы](../concepts/explore-resources.md).