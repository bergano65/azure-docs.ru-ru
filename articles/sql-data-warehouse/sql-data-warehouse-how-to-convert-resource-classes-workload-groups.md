---
title: Преобразование класса ресурсов в группу рабочей нагрузки
description: Узнайте, как создать группу рабочей нагрузки, похожую на класс ресурсов в хранилище данных SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685708"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Преобразование классов ресурсов в группы рабочей нагрузки
Группы рабочей нагрузки предоставляют механизм для изоляции и хранения системных ресурсов.  Кроме того, группы рабочей нагрузки позволяют задавать правила выполнения для запросов, выполняемых в них.  Правило выполнения запроса с превышением времени ожидания позволяет отменить невыполненные запросы без вмешательства пользователя.  В этой статье объясняется, как взять существующий класс ресурсов и создать группу рабочей нагрузки с аналогичной конфигурацией.  Кроме того, добавляется Необязательное правило времени ожидания запроса.

## <a name="understanding-the-existing-resource-class-configuration"></a>Основные сведения о существующей конфигурации класса ресурсов
Группам рабочей нагрузки требуется параметр с именем `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, указывающий процент общих системных ресурсов, выделенных для каждого запроса.  Выделение ресурсов выполняется для [классов ресурсов](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) путем выделения слотов выдачи.  Чтобы определить значение, которое необходимо указать для `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, используйте динамическое административное представление sys. DM _workload_management_workload_groups_stats <link tbd>.  Например, приведенный ниже запрос запроса возвращает значение, которое можно использовать для параметра `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, чтобы создать группу рабочей нагрузки, подобную staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Группы рабочей нагрузки работают в процентах от общего количества системных ресурсов.  

Поскольку группы рабочей нагрузки работают на основе процента общих системных ресурсов по мере увеличения и уменьшения масштаба, процент ресурсов, выделенных для статических классов ресурсов относительно общих изменений системных ресурсов.  Например, staticrc40 в DW1000c выделяет 9,6% общих системных ресурсов.  В DW2000c выделяются 19,2%.  Эта модель аналогична, если вы хотите увеличить параллелизм и выделить больше ресурсов на запрос.   

## <a name="create-workload-group"></a>Создание группы рабочей нагрузки
С помощью известного `REQUEST_MIN_RESOURCE_GRANT_PERCENT`можно использовать синтаксис CREATE Рабочей группы <link> для создания группы рабочей нагрузки.  При необходимости можно указать `MIN_PERCENTAGE_RESOURCE` больше нуля, чтобы изолировать ресурсы для группы рабочей нагрузки.  Кроме того, при необходимости можно указать `CAP_PERCENTAGE_RESOURCE` менее 100, чтобы ограничить объем ресурсов, которые может использовать группа рабочей нагрузки.  

В приведенном ниже примере задается `MIN_PERCENTAGE_RESOURCE`, чтобы выделить 9,6% системных ресурсов для `wgDataLoads` и гарантировать, что один запрос сможет выполняться все время.  Кроме того, `CAP_PERCENTAGE_RESOURCE` имеет значение 38,4% и ограничивает эту группу рабочей нагрузки четырьмя одновременными запросами.  Если задать для параметра `QUERY_EXECUTION_TIMEOUT_SEC` значение 3600, все запросы, выполняемые более 1 часа, будут автоматически отменены.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Создание классификатора
Ранее сопоставление запросов с классами ресурсов выполнялось с помощью процедуры [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Чтобы обеспечить ту же функциональность и сопоставлять запросы с группами рабочей нагрузки, используйте синтаксис [классификатора рабочей нагрузки](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) .  Использование функции sp_addrolemember позволило вам сопоставлять ресурсы с запросом на основе имени входа.  Классификатор предоставляет дополнительные параметры помимо имени входа, например:
    - label
    - session
    - во время, приведенном ниже примере, назначаются запросы из имени входа `AdfLogin`, для которого также задана [Метка параметра](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) `factloads` группе рабочей нагрузки, созданной ранее `wgDataLoads`.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Тестирование с помощью примера запроса
Ниже приведен пример запроса и запрос динамического административного представления, чтобы убедиться, что группа рабочей нагрузки и классификатор настроены правильно.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Дальнейшие действия

Изоляция рабочей нагрузки — компоновка. как создать группу рабочей нагрузки — компоновка описана