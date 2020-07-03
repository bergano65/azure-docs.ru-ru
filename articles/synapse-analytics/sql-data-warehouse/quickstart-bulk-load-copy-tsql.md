---
title: Краткое руководство. Выполнение массовой загрузки данных с помощью одной инструкции T-SQL
description: Массовая загрузка данных с помощью инструкции COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d39b3085a802ca0ff745ab1f63f4a8fba966ea48
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115004"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Краткое руководство. Массовая загрузка данных с помощью инструкции COPY

В этом кратком руководстве вы выполните массовую загрузку данных в пул SQL с помощью простой и гибкой [инструкции COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) для приема данных с высокой пропускной способностью. Инструкция COPY является рекомендуемой служебной программой загрузки, поскольку она позволяет легко и гибко загружать данные, предоставляя им функциональные возможности, которые позволяют:

- Выполнять загрузку пользователям с более низкими привилегиями, без ограничения разрешений на управление хранилищем данных
- Использовать только одну инструкцию T-SQL без необходимости создавать дополнительные объекты базы данных.
- Использовать более детализированную модель разрешений без предоставления ключей учетной записи хранения с помощью подписанных URL-адресов (SAS).
- Указать другую учетную запись хранения для расположения ERRORFILE (REJECTED_ROW_LOCATION).
- Настроить значения по умолчанию для каждого целевого столбца и указать поля исходных данных для загрузки в определенные целевые столбцы.
- Указать пользовательский символ конца строки для CSV-файлов.
- Отменить разделители строк, полей и строк для CSV-файлов.
- Использовать форматы даты SQL Server для CSV-файлов.
- Указать подстановочные знаки и несколько файлов в пути места хранения.

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве предполагается, что у вас уже есть пул SQL. Если пул SQL не создан, используйте краткое руководство [Создание пула SQL Synapse и отправка в него запросов, используя портал Azure](create-data-warehouse-portal.md).

## <a name="create-the-target-table"></a>Создание целевой таблицы

В этом примере мы будем загружать данные из базы данных такси в Нью-Йорке. Мы загрузим таблицу под названием "Поездка", которая представляет собой поездки на такси, осуществленные в течение одного года. Для создания таблицы выполните следующие действия.

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Выполнение инструкции COPY

Выполните следующую инструкцию COPY, которая будет загружать данные из учетной записи хранилища BLOB-объектов Azure в таблицу "Поездка".

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Мониторинг загрузки

Проверьте, выполняется ли ваша загрузка, периодически выполняя следующий запрос:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Дальнейшие действия

- Рекомендации по загрузке данных см. в статье [Рекомендации по загрузке данных с помощью пула Synapse SQL](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Сведения об управлении ресурсами для загрузки данных см. в кратком руководстве [Настройка изоляции рабочих нагрузок с помощью T-SQL](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
