---
title: Учетные записи хранения, принадлежащие клиенту, для приема данных журнала
description: Используйте собственную учетную запись хранения для приема данных журнала в рабочей области Log Analytics в Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 78cdf1798d9176c9d3c7b3705a11dccde5e47e67
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077111"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Учетные записи хранения, принадлежащие клиенту, для приема данных журнала в Azure Monitor

Azure Monitor использует учетные записи хранения в процессе приема некоторых типов данных, таких как [пользовательские журналы](data-sources-custom-logs.md) и некоторые [журналы Azure](./diagnostics-extension-logs.md). В процессе приема данных журналы сначала отправляются в учетную запись хранения, а затем принимаются в Log Analytics или Application Insights. Если вы хотите управлять данными во время приема, можно использовать собственные учетные записи хранения вместо хранилища, управляемого службой. Использование собственной учетной записи хранения позволяет управлять доступом, содержимым, шифрованием и хранением журналов во время приема. Это называется использованием собственного хранилища или BYOS. 

Одним из сценариев, требующих BYOS, является сетевая изоляция с помощью Приватного канала. При использовании виртуальной сети сетевая изоляция часто является обязательным требованием, а доступ к общедоступному Интернету ограничен. В таких случаях доступ к хранилищу служб Azure Monitor для приема журнала полностью заблокирован или не рекомендован. Вместо этого данные журналов должны быть переданы или легко доступны через учетную запись хранения, принадлежащую клиенту, в виртуальной сети.

Другой сценарий — это шифрование журналов с ключами, управляемыми клиентом (CMK). Клиенты могут зашифровать данные журнала с помощью CMK в кластерах, хранящих журналы. Тот же ключ можно использовать для шифрования журналов в процессе приема.

## <a name="data-types-supported"></a>Поддерживаемые типы данных

К типам данных, получаемым из учетной записи хранения, относятся следующие. Дополнительные сведения о приеме этих типов данных см. в статье [Сбор данных из расширения диагностики Azure в журналах Azure Monitor](./diagnostics-extension-logs.md).

| Тип | Сведения таблицы |
|:-----|:------------------|
| Журналы IIS | Большие двоичные объекты: файлы журналов wad-iis|
|Журналы событий Windows | Таблица. WADWindowsEventLogsTable |
| Системный журнал | Таблица. LinuxsyslogVer2v0 |
| Журналы трассировки событий Windows | Таблица. WADETWEventTable|
| Service Fabric | Таблица. WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Пользовательские журналы | Недоступно |
| Файлы дампа Watson Центра безопасности Azure | Недоступно|  

## <a name="storage-account-requirements"></a>Требования к учетной записи хранения 
Учетная запись хранения должна соответствовать следующим требованиям:

- быть доступной для ресурсов в вашей виртуальной сети, которая записывает журналы в хранилище;
- находиться в том же регионе, что и рабочая область, с которой она связан;
- Разрешить Azure Monitor доступ. Если вы решили ограничить доступ к учетной записи хранения для выбора сетей, обязательно разрешите это исключение: *разрешить доверенным службам Майкрософт доступ к этой учетной записи хранения*.

## <a name="process-to-configure-customer-owned-storage"></a>Процесс настройки хранилища, принадлежащего клиенту
Базовый процесс использования собственной учетной записи хранения для приема выглядит следующим образом:

1. Создайте учетную запись хранения или выберите существующую учетную запись.
2. Свяжите учетную запись хранения с рабочей областью Log Analytics.
3. Управляйте хранилищем, проверив его загруженность и хранение данных, чтобы убедиться, что оно работает правильно.

Использование REST API — это единственный метод, доступный для создания и удаления ссылок. Сведения об определенном запросе API для каждого процесса приведены в следующих разделах.

## <a name="command-line-and-rest-api"></a>Командная строка и REST API

### <a name="command-line"></a>Командная строка
Чтобы создать связанные учетные записи хранения и управлять ими, используйте команду [AZ Monitor журнала-Analytics Workspace Link-Storage](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage). Эта команда может связывать учетные записи хранения и отменять связи между ними из рабочей области и перечислять связанные учетные записи хранения.

### <a name="request-and-cli-values"></a>Значения запроса и CLI

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson — используйте это значение для файлов дампа Azure Watson Центра безопасности Azure.
- CustomLogs — используйте это значение для следующих типов данных:
  - Пользовательские журналы
  - Журналы IIS
  - события (Windows);
  - системный журнал (Linux);
  - журналы трассировки событий Windows;
  - события Service Fabric;
  - данные оценки.  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Это значение использует следующую структуру:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>Получить связанные учетные записи хранения для всех типов источников данных

#### <a name="api-request"></a>Запрос API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Ответ

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Получение связанных учетных записей хранения для определенного типа источника данных

#### <a name="api-request"></a>Запрос API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Ответ 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>Создание или изменение ссылки

После связывания учетной записи хранения с рабочей областью Log Analytics начнет использовать ее вместо учетной записи хранения, которая принадлежит службе. Вы можете одновременно зарегистрировать список учетных записей хранения и использовать одну и ту же учетную запись хранения для нескольких рабочих областей.

Если рабочая область обрабатывает как ресурсы в виртуальной сети, так и ресурсы за ее пределами, следует убедиться, что она не отклоняет трафик, поступающий из Интернета. Необходимо, чтобы у хранилища были те же параметры, что и у рабочей области. Оно должно быть доступным для ресурсов за пределами виртуальной сети. 

### <a name="api-request"></a>Запрос API

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Полезные данные

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Ответ

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Удаление привязки учетной записи хранения
Если вы решили больше не использовать учетную запись хранения для приема или замены используемой рабочей области, необходимо отменить привязку хранилища с рабочей областью.

Отмена привязки всех учетных записей хранения с рабочими областями означает, что прием данных будет происходить на основе учетных записей хранения, управляемых службой. Если агенты выполняются в виртуальной сети с ограниченным доступом к Интернету, ожидается, что прием данных завершится сбоем. У рабочей области должна быть связанная учетная запись хранения, доступная из отслеживаемых ресурсов.

Перед тем как удалять учетную запись хранения убедитесь, что все содержащиеся в ней данные были приняты в рабочую область. В качестве меры предосторожности обеспечьте доступность учетной записи хранения после установки связи с альтернативным хранилищем. Удаляйте ее только после приема всего содержимого и когда новые данные будут записаны в новую подключенную учетную запись хранения.


### <a name="api-request"></a>Запрос API
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Замена учетной записи хранения

Чтобы заменить учетную запись хранения, используемую для приема, сначала создайте ссылку для новой учетной записи хранения. Агенты ведения журнала получают обновленную конфигурацию и начинают отправлять данные в новое хранилище.

Затем отмените привязку со старой учетной записью хранения, чтобы агенты перестали записывать данные в удаленную учетную запись. Процесс приема данных позволяет считывать данные из этой учетной записи до тех пор, пока все они не будут приняты. Не удаляйте учетную запись хранения, пока не будут приняты данные всех журналов.

Конфигурация агента обновится через несколько минут, и будет использоваться новое хранилище.

## <a name="manage-storage-account"></a>Управление учетной записью хранения

### <a name="load"></a>Загрузить

Учетные записи хранения могут обрабатывать определенную нагрузку запросов на чтение и запись, прежде чем начать регулировать запросы. Регулирование влияет на время, затрачиваемое на прием данных журналов, и может привести к потере данных. Если хранилище перегружено, зарегистрируйте дополнительные учетные записи хранения и распределите нагрузку между ними. 

### <a name="related-charges"></a>Связанные расходы

Плаза за учетные записи хранения взимается по объему хранимых данных, типу хранилища и типу избыточности. Дополнительные сведения см. в статьях [Цены на хранение блочных BLOB-объектов](https://azure.microsoft.com/pricing/details/storage/blobs/) и [Цены на хранилище таблиц Azure](https://azure.microsoft.com/pricing/details/storage/tables/).

Если зарегистрированная учетная запись хранения рабочей области находится в другом регионе, плата за исходящий трафик будет взиматься в соответствии со [стоимостью пропускной способности](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о настройке частной ссылки см. в статье [Использование частной связи Azure для безопасного подключения сетей к Azure Monitor](private-link-security.md)
