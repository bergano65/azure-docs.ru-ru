---
title: Преобразование класса ресурсов в рабочую группу
description: Узнайте, как создать группу рабочей нагрузки, аналогичную классу ресурсов в Хранилище данных Azure S'L.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8cee874106598c7d81b923d7dd32ba91902d9326
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745185"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Преобразование классов ресурсов в группы рабочей нагрузки

Группы рабочей нагрузки обеспечивают механизм изоляции и сдерживания системных ресурсов.  Кроме того, группы рабочей нагрузки позволяют устанавливать правила выполнения для запросов, работающих в них.  Правило тайм-аута запроса позволяет отменять запросы без вмешательства пользователя.  В этой статье объясняется, как взять существующий класс ресурсов и создать рабочую группу с аналогичной конфигурацией.  Кроме того, добавляется дополнительное правило тайм-аута запроса.

## <a name="understanding-the-existing-resource-class-configuration"></a>Понимание существующей конфигурации класса ресурсов

Группы рабочей нагрузки требуют параметра, называемого, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` который определяет процент от общих ресурсов системы, выделяемых на запрос.  Распределение ресурсов осуществляется для [классов ресурсов](resource-classes-for-workload-management.md#what-are-resource-classes) путем выделения слотов для параллелизма.  Чтобы определить значение, `REQUEST_MIN_RESOURCE_GRANT_PERCENT`чтобы указать для, <link tbd> используйте sys.dm_workload_management_workload_groups_stats DMV.  Например, ниже запрос запросвозвращает значение, которое может `REQUEST_MIN_RESOURCE_GRANT_PERCENT` быть использовано для параметра для создания рабочей группы, аналогичной staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Группы рабочей нагрузки работают на основе доли общих системных ресурсов.  

Поскольку группы рабочей нагрузки работают на основе процентной доли общих системных ресурсов по мере масштабирования и вниз, процент ресурсов, выделяемых на статические классы ресурсов, по отношению к общим изменениям ресурсов системы.  Например, staticrc40 на DW1000c выделяет 9,6% общих ресурсов системы.  На DW2000c выделяется 19,2%.  Эта модель аналогична, если вы хотите масштабировать для параллелизма по сравнению с выделением большего количества ресурсов на запрос.

## <a name="create-workload-group"></a>Создание рабочей группы

С известным, `REQUEST_MIN_RESOURCE_GRANT_PERCENT`вы можете использовать СИНтаксис CREATE WORKLOAD GROUP <link> для создания рабочей группы нагрузки.  Можно дополнительно `MIN_PERCENTAGE_RESOURCE` указать, что больше нуля, чтобы изолировать ресурсы для группы рабочей нагрузки.  Кроме того, можно `CAP_PERCENTAGE_RESOURCE` дополнительно указать менее 100 ресурсов, чтобы ограничить объем ресурсов, которые может потреблять рабочая группа.  

Ниже приведенпримерк, который `MIN_PERCENTAGE_RESOURCE` предусматривает выделение 9,6% ресурсов системы `wgDataLoads` и гарантирует, что один запрос сможет работать все время.  Кроме того, `CAP_PERCENTAGE_RESOURCE` установлен на 38,4% и ограничивает эту рабочую группу до четырех одновременных запросов.  При установке `QUERY_EXECUTION_TIMEOUT_SEC` параметра до 3600 любой запрос, который выполняется более 1 часа, будет автоматически отменен.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Создание классификатора

Ранее отображение запросов в классах ресурсов осуществлялось с [помощью sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Для достижения той же функциональности и картографических запросов для групп рабочей нагрузки используйте синтаксис [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Использование sp_addrolemember позволяет отображать ресурсы только к запросу на основе входа.  Классификатор предоставляет дополнительные опции, помимо входа, такие как:
    - label
    - сеанс
    - время приведенный ниже пример назначает `AdfLogin` запросы из входа, которые `factloads` также имеют `wgDataLoads` [OPTION LABEL,](sql-data-warehouse-develop-label.md) установленный для группы рабочей нагрузки, созданной выше.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Тест с образцом запроса

Ниже приведены пример запроса и dMV-запрос для обеспечения правильной настройки группы рабочей нагрузки и классификатора.

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

- [Изоляция рабочей нагрузки](sql-data-warehouse-workload-isolation.md)
- [Как создать рабочую группу-ссылку](quickstart-configure-workload-isolation-tsql.md)
