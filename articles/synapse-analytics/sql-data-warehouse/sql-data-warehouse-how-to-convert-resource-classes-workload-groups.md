---
title: Преобразование класса ресурсов в группу рабочей нагрузки
description: Узнайте, как создать группу рабочей нагрузки, похожую на класс ресурсов в хранилище данных SQL Azure.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d73ba8f21fe7731fb751d42a8497ff8e1ebba7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383626"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Преобразование классов ресурсов в группы рабочей нагрузки

Группы рабочей нагрузки предоставляют механизм для изоляции и хранения системных ресурсов.  Кроме того, группы рабочей нагрузки позволяют задавать правила выполнения для запросов, выполняемых в них.  Правило выполнения запроса с превышением времени ожидания позволяет отменить невыполненные запросы без вмешательства пользователя.  В этой статье объясняется, как взять существующий класс ресурсов и создать группу рабочей нагрузки с аналогичной конфигурацией.  Кроме того, добавляется Необязательное правило времени ожидания запроса.

> [!NOTE]
> Рекомендации по использованию групп рабочей нагрузки и классов ресурсов см. в разделе [назначение классов ресурсов с помощью классификаторов](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) в документе концепции [классификации рабочей нагрузки](sql-data-warehouse-workload-classification.md) .

## <a name="understanding-the-existing-resource-class-configuration"></a>Основные сведения о существующей конфигурации класса ресурсов

Для групп рабочей нагрузки требуется параметр `REQUEST_MIN_RESOURCE_GRANT_PERCENT` с именем, указывающий процент общих системных ресурсов, выделенных для каждого запроса.  Выделение ресурсов выполняется для [классов ресурсов](resource-classes-for-workload-management.md#what-are-resource-classes) путем выделения слотов выдачи.  Чтобы определить значение, которое необходимо указать `REQUEST_MIN_RESOURCE_GRANT_PERCENT`для, используйте динамическое административное представление sys. dm_workload_management_workload_groups_stats <link tbd> .  Например, приведенный ниже запрос запроса возвращает значение, которое можно использовать для `REQUEST_MIN_RESOURCE_GRANT_PERCENT` параметра, чтобы создать группу рабочей нагрузки, подобную staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Группы рабочей нагрузки работают в процентах от общего количества системных ресурсов.  

Поскольку группы рабочей нагрузки работают на основе процента общих системных ресурсов по мере увеличения и уменьшения масштаба, процент ресурсов, выделенных для статических классов ресурсов относительно общих изменений системных ресурсов.  Например, staticrc40 в DW1000c выделяет 9,6% общих системных ресурсов.  В DW2000c выделяются 19,2%.  Эта модель аналогична, если вы хотите увеличить параллелизм и выделить больше ресурсов на запрос.

## <a name="create-workload-group"></a>Создание группы рабочей нагрузки

Используя известный `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, можно создать группу рабочей нагрузки с помощью СИНТАКСИСА Create Рабочей группы <link> .  При необходимости можно указать значение `MIN_PERCENTAGE_RESOURCE` , большее нуля, чтобы изолировать ресурсы для группы рабочей нагрузки.  Кроме того, при необходимости можно указать `CAP_PERCENTAGE_RESOURCE` менее 100, чтобы ограничить объем ресурсов, которые может использовать группа рабочей нагрузки.  

В приведенном ниже примере `MIN_PERCENTAGE_RESOURCE` задается, чтобы выделить 9,6% системных ресурсов `wgDataLoads` и гарантировать, что один запрос сможет выполняться все время.  Кроме того `CAP_PERCENTAGE_RESOURCE` , имеет значение 38,4% и ограничивает эту группу рабочей нагрузки четырьмя одновременными запросами.  Если задать для `QUERY_EXECUTION_TIMEOUT_SEC` параметра значение 3600, любой запрос, выполняемый более 1 часа, будет автоматически отменен.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Создание классификатора

Ранее сопоставление запросов с классами ресурсов выполнялось [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Чтобы обеспечить ту же функциональность и сопоставлять запросы с группами рабочей нагрузки, используйте синтаксис [классификатора рабочей нагрузки](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  С помощью sp_addrolemember вы можете сопоставлять ресурсы с запросом на основе имени входа.  Классификатор предоставляет дополнительные параметры помимо имени входа, например:
    - label
    - сеанс
    - во время, приведенном ниже примере, к `AdfLogin` имени входа назначаются запросы, для которых в `factloads` качестве [метки параметра](sql-data-warehouse-develop-label.md) задана группа `wgDataLoads` рабочей нагрузки, созданная ранее.

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

## <a name="next-steps"></a>Дальнейшие шаги

- [Изоляция рабочей нагрузки](sql-data-warehouse-workload-isolation.md)
- [Создание группы рабочей нагрузки](quickstart-configure-workload-isolation-tsql.md)
- [Классификатор CREATE WORKLOAD (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
