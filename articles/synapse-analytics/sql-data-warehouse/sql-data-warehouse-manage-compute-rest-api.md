---
title: Приостановка, возобновление и масштабирование с помощью REST API
description: Управление вычислительными ресурсами в хранилище данных Azure Synapse Analytics с помощью REST API.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: bbb8e82710b4c8ca7736b53d427b3880faf2be05
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213284"
---
# <a name="rest-apis-for-azure-synapse-analytics-data-warehouse"></a>REST API для хранилища данных Azure Synapse Analytics

REST API для управления вычислительными ресурсами в хранилище данных Azure Synapse Analytics.

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов

Чтобы изменить число DWU, используйте REST API [создания или обновления базы данных](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). В приведенном ниже примере для базы данных MySQLDW, размещенной на сервере MyServer, устанавливается значение DW1000. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Приостановка работы вычислительных ресурсов

Чтобы приостановить базу данных, используйте REST API [приостановки базы данных](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). В приведенном ниже примере приостанавливается работа базы данных с именем Database02, размещенной на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Возобновление работы вычислительных ресурсов

Чтобы запустить базу данных, используйте REST API [возобновления базы данных](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). В приведенном ниже примере запускается база данных с именем Database02, размещенная на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Проверка состояния базы данных

> [!NOTE]
> Сейчас проверка состояния базы данных может вернуть значение В СЕТИ, пока база данных завершает рабочий процесс в сети, что приводит к ошибкам подключения. Может потребоваться добавить в код приложения задержку в 2–3 минуты, если вы используете этот вызов API для активации попыток подключения.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Получение информации о расписании обслуживания

Проверьте расписание обслуживания, заданное для хранилища данных.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Составление расписания обслуживания

Составление и обновление расписания обслуживания для существующего хранилища данных.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Управление вычислительными ресурсами в хранилище данных SQL Azure](sql-data-warehouse-manage-compute-overview.md).
