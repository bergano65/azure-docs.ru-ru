---
title: Изоляция рабочей нагрузки
description: Руководство по настройке изоляции рабочей нагрузки с помощью групп рабочей нагрузки в Azure синапсе Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5d81dc1f4da6e952061496fa348d0f8e87b00b81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80742971"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Изоляция группы рабочей нагрузки Azure синапсе Analytics (Предварительная версия)

В этой статье объясняется, как можно использовать группы рабочей нагрузки для настройки изоляции рабочей нагрузки, хранения ресурсов и применения правил среды выполнения для выполнения запросов.

## <a name="workload-groups"></a>Группы рабочей нагрузки

Группы рабочей нагрузки — это контейнеры для набора запросов, которые определяют, как Управление рабочей нагрузкой, включая изоляцию рабочей нагрузки, настраивается в системе.  Группы рабочей нагрузки создаются с помощью синтаксиса [CREATE Рабочей группы](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  Простая конфигурация управления рабочей нагрузкой может управлять загрузкой данных и пользовательскими запросами.  Например, группа рабочей нагрузки с именем `wgDataLoads` будет определять аспекты рабочей нагрузки для загрузки данных в систему. Кроме того, группа рабочей нагрузки `wgUserQueries` с именем будет определять аспекты рабочей нагрузки для пользователей, выполняющих запросы для чтения данных из системы.

В следующих разделах будут выводиться сведения о том, как группы рабочей нагрузки предоставляют возможность определения изоляции, включения, запроса определения ресурса и соблюдения правил выполнения.

## <a name="workload-isolation"></a>Изоляция рабочей нагрузки

Изоляция рабочей нагрузки означает, что ресурсы зарезервированы, исключительно для группы рабочей нагрузки.  Изоляция рабочей нагрузки достигается путем настройки для параметра MIN_PERCENTAGE_RESOURCE значение больше нуля в синтаксисе [CREATE Рабочей группы](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  Для рабочих нагрузок непрерывного выполнения, которые должны соответствовать строгим соглашениям об уровне обслуживания, изоляция гарантирует, что ресурсы всегда доступны для группы рабочей нагрузки.

Настройка изоляции рабочей нагрузки неявно определяет гарантированный уровень параллелизма. Например, группа рабочей нагрузки с параметром `MIN_PERCENTAGE_RESOURCE` , равным 30% `REQUEST_MIN_RESOURCE_GRANT_PERCENT` и равным 2%, гарантирует 15 параллельных операций.  Уровень параллелизма гарантируется из-за того, что 15-2% ресурсов в группе рабочей нагрузки зарезервированы в любое время (независимо от `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` того, как настроена).  Если `REQUEST_MAX_RESOURCE_GRANT_PERCENT` значение больше `REQUEST_MIN_RESOURCE_GRANT_PERCENT` и `CAP_PERCENTAGE_RESOURCE` больше, чем `MIN_PERCENTAGE_RESOURCE` дополнительные ресурсы, добавляются по запросу.  Если `REQUEST_MAX_RESOURCE_GRANT_PERCENT` и `REQUEST_MIN_RESOURCE_GRANT_PERCENT` равны и `CAP_PERCENTAGE_RESOURCE` больше `MIN_PERCENTAGE_RESOURCE`, возможна дополнительная степень параллелизма.  Рассмотрим метод, приведенный ниже, для определения гарантированного параллелизма.

[Гарантированный параллелизм] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Для min_percentage_resource существуют определенные минимально приемлемые значения уровня обслуживания.  Дополнительные сведения см. в разделе [действующие значения](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values) для получения дополнительных сведений.

При отсутствии изоляции рабочей нагрузки запросы работают в [общем пуле](#shared-pool-resources) ресурсов.  Доступ к ресурсам в общем пуле не гарантируется и назначается на уровне [важности](sql-data-warehouse-workload-importance.md) .

Настройка изоляции рабочей нагрузки должна выполняться с осторожностью, так как ресурсы выделяются группе рабочей нагрузки, даже если в группе рабочей нагрузки нет активных запросов. Чрезмерная Настройка изоляции может привести к снижению общего использования системы.

Пользователи должны избегать решения для управления рабочей нагрузкой, которое настраивает изоляцию нагрузки 100%: уровень изоляции 100% достигается, когда сумма min_percentage_resource, настроенная во всех группах рабочей нагрузки, равна 100%.  Этот тип конфигурации имеет более строгие жесткие настройки, что приводит к небольшому объему запросов ресурсов, которые случайно не были неправильно классифицированы. Существует возможность разрешить выполнение одного запроса из групп рабочей нагрузки, для которых не настроена изоляция. Ресурсы, выделенные для этого запроса, будут отображаться в системных административных представлениях как ноль и поsmallrc уровень предоставления ресурсов из системных зарезервированных ресурсов.

> [!NOTE]
> Чтобы обеспечить оптимальное использование ресурсов, рассмотрим решение для управления рабочей нагрузкой, которое использует некоторую изоляцию, чтобы обеспечить соблюдение соглашений об уровне обслуживания и смешанное использование общих ресурсов, доступ к которым осуществляется на основе [важности рабочей нагрузки](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Включение рабочей нагрузки

Включение рабочей нагрузки означает ограничение объема ресурсов, которые может использовать группа рабочей нагрузки.  Включение рабочей нагрузки достигается путем настройки параметра CAP_PERCENTAGE_RESOURCE на значение меньше 100 в синтаксисе [CREATE Рабочей группы](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  Рассмотрим ситуацию, когда пользователям требуется доступ для чтения к системе, чтобы они могли выполнять анализ гипотетических случаев с помощью специальных запросов.  Эти типы запросов могут оказать негативное влияние на другие рабочие нагрузки, работающие в системе.  Настройка включения гарантирует, что объем ресурсов ограничен.

Настройка включения рабочей нагрузки неявно определяет максимальный уровень параллелизма.  Если CAP_PERCENTAGE_RESOURCE задано значение 60%, а для REQUEST_MIN_RESOURCE_GRANT_PERCENT задано значение 1%, для группы рабочей нагрузки разрешено до уровня параллелизма не более 60.  Рассмотрим метод, приведенный ниже, чтобы определить максимальную степень параллелизма:

[Max Concurrency] = [`CAP_PERCENTAGE_RESOURCE`]/[]`REQUEST_MIN_RESOURCE_GRANT_PERCENT`

> [!NOTE]
> Эффективное CAP_PERCENTAGE_RESOURCE группы рабочей нагрузки не достигает 100%, когда создаются группы рабочей нагрузки с MIN_PERCENTAGE_RESOURCE на уровне больше нуля.  Действующие значения времени выполнения см. в разделе [sys. dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="resources-per-request-definition"></a>Ресурсы на определение запроса

Группы рабочей нагрузки предоставляют механизм для определения минимального и максимального объема ресурсов, выделяемых для каждого запроса с помощью REQUEST_MIN_RESOURCE_GRANT_PERCENT и REQUEST_MAX_RESOURCE_GRANT_PERCENT параметров в синтаксисе [CREATE Рабочей группы](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  В этом случае ресурсами являются ЦП и память.  Настройка этих значений определяет, сколько ресурсов и какой уровень параллелизма может быть достигнут в системе.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT — это необязательный параметр, по умолчанию имеющий то же значение, что и для REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Как и при выборе класса ресурсов, Настройка REQUEST_MIN_RESOURCE_GRANT_PERCENT задает значение для ресурсов, используемых запросом.  Объем ресурсов, указанных в наборе, гарантированно выделен для запроса, прежде чем он начнет выполнение.  Для клиентов, выполняющих миграцию из классов ресурсов в группы рабочей нагрузки, рассмотрите статью [инструкции](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) по сопоставлению классов ресурсов с группами рабочей нагрузки в качестве отправной точки.

При настройке REQUEST_MAX_RESOURCE_GRANT_PERCENT значение больше REQUEST_MIN_RESOURCE_GRANT_PERCENT позволяет системе выделить больше ресурсов на запрос.  При планировании запроса система определяет фактическое выделение ресурсов для запроса, который находится между REQUEST_MIN_RESOURCE_GRANT_PERCENT и REQUEST_MAX_RESOURCE_GRANT_PERCENT, в зависимости от доступности ресурсов в общем пуле и текущей нагрузки в системе.  При планировании запроса ресурсы должны существовать в [общем пуле](#shared-pool-resources) ресурсов.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT и REQUEST_MAX_RESOURCE_GRANT_PERCENT имеют действующие значения, зависящие от действующих MIN_PERCENTAGE_RESOURCE и CAP_PERCENTAGE_RESOURCE значений.  Действующие значения времени выполнения см. в разделе [sys. dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="execution-rules"></a>Правила выполнения

В системах автоматизированной системы отчетности клиенты могут случайно выполнять неконтролируемые запросы, которые серьезно влияют на производительность других пользователей.  Системные администраторы вынуждены тратить время на прерывание запросов, чтобы освободить системные ресурсы.  Группы рабочей нагрузки предоставляют возможность настройки правила времени ожидания выполнения запроса для отмены запросов, превышающих указанное значение.  Правило настраивается с помощью `QUERY_EXECUTION_TIMEOUT_SEC` параметра в синтаксисе [CREATE Рабочей группы](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="shared-pool-resources"></a>Ресурсы общего пула

Ресурсы общего пула — это ресурсы, не настроенные для изоляции.  Группы рабочей нагрузки с MIN_PERCENTAGE_RESOURCE, для которых задано нулевое значение, используют ресурсы в общем пуле для выполнения запросов.  Группы рабочей нагрузки с CAP_PERCENTAGE_RESOURCE больше MIN_PERCENTAGE_RESOURCE также используют общие ресурсы.  Объем ресурсов, доступных в общем пуле, вычисляется следующим образом.

[Общий пул] = 100-[сумма `MIN_PERCENTAGE_RESOURCE` по всем группам рабочей нагрузки]

Доступ к ресурсам в общем пуле распределяется по [важности](sql-data-warehouse-workload-importance.md) .  Запросы с одинаковым уровнем важности будут получать доступ к ресурсам общего пула на основе первых походящих и начальных адресов.

## <a name="next-steps"></a>Дальнейшие шаги

- [Краткое руководство. Настройка изоляции рабочей нагрузки](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Преобразование классов ресурсов в группы рабочей нагрузки](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Портал управления мониторинга рабочей нагрузки](sql-data-warehouse-workload-management-portal-monitor.md).  
