---
title: Приостановка, возобновление и масштабирование ресурсов в хранилище данных SQL Azure с помощью REST | Документация Майкрософт
description: Управление вычислительными ресурсами в хранилище данных SQL с помощью REST API.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b1763bf08b15a5b40619522ee212eefe3a72ee76
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455689"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST API для хранилища данных Azure SQL
REST API для управления вычислительными ресурсами в хранилище данных Azure SQL.

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов
Чтобы изменить число DWU, используйте REST API [создания или обновления базы данных](/rest/api/sql/databases/createorupdate). В приведенном ниже примере для базы данных MySQLDW, размещенной на сервере MyServer, устанавливается значение DW1000. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

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

Чтобы приостановить базу данных, используйте API REST [Приостановка базы данных](/rest/api/sql/databases/pause) . В приведенном ниже примере приостанавливается работа базы данных с именем Database02, размещенной на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Возобновление работы вычислительных ресурсов

Чтобы запустить базу данных, используйте API REST [Возобновление базы данных](/rest/api/sql/databases/resume) . В приведенном ниже примере запускается база данных с именем Database02, размещенная на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Проверка состояния базы данных

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в статье [Управление вычислительными ресурсами в хранилище данных SQL Azure](sql-data-warehouse-manage-compute-overview.md).

