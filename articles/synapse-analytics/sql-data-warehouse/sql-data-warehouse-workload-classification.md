---
title: Классификация рабочих нагрузок
description: Руководство по использованию классификации для управления параллелизмом, важностью и вычислением ресурсов для запросов в Azure Synapse Analytics.
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
ms.openlocfilehash: e7aa0c402878c994aabe4e12d811a99e300d7e67
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743657"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Классификация рабочей нагрузки Azure Synapse Analytics

В этой статье объясняется процесс классификации рабочей нагрузки при назначении группы рабочей нагрузки и важность входящих запросов с пулами Synapse S'L в Azure Synapse.

## <a name="classification"></a>Классификация

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Классификация управления рабочей нагрузкой позволяет применять политики рабочей нагрузки к запросам путем присвоения [классов ресурсов](resource-classes-for-workload-management.md#what-are-resource-classes) и [важности.](sql-data-warehouse-workload-importance.md)

Хотя существует множество способов классификации рабочих нагрузок складирования данных, наиболее простой и распространенной классификацией является нагрузка и запрос. Данные загружаются с помощью вставки, обновления и удаления инструкций.  Вы задавили запрос данных с помощью выбранных. Решение для складирования данных часто имеет рабочую политику для деятельности нагрузки, например назначение более высокого класса ресурсов с большим количеством ресурсов. К запросам может применяться другая политика рабочей нагрузки, например, более низкая важность по сравнению с действиями по нагрузке.

Можно также подклассифицировать рабочие нагрузки на грузы и запросы. Подклассификация позволяет больше контролировать рабочие нагрузки. Например, рабочие нагрузки запроса могут состоять из обновлений кубиков, запросов панели мониторинга или специальных запросов. Можно классифицировать каждую из этих рабочих нагрузок запроса с различными классами ресурсов или настройками важности. Нагрузка также может извлечь выгоду из подклассификации. Большие преобразования могут быть назначены более крупным классам ресурсов. Более важное значение можно использовать для обеспечения того, чтобы ключевые данные о продажах были погрузчиком перед данными о погоде или каналом социальных данных.

Не все операторы классифицируются, поскольку они не требуют ресурсов или требуют важности для влияния на исполнение.  Команды DBCC, заявления BEGIN, COMMIT и ROLLBACK TRANSACTION не классифицируются.

## <a name="classification-process"></a>Процесс классификации

Классификация для пула Synapse S'L в Azure Synapse достигается сегодня путем присвоения пользователям роли, которая имеет соответствующий класс ресурсов, присвоенный ему с помощью [sp_addrolemember.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) Возможность характеристики запросов, выходящих за рамки входа в класс ресурсов, ограничена этой возможностью. Более богатый метод классификации теперь доступен с синтаксисом [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  С помощью этого синтаксиса пользователи пула Synapse S'L могут присвоить значение и размер системных ресурсов, назначенных запросу по параметру. `workload_group`

> [!NOTE]
> Классификация оценивается на основе запроса. Несколько запросов за один сеанс можно классифицировать по-разному.

## <a name="classification-weighting"></a>Классификационное взвешивание

В рамках процесса классификации проводится взвешивание для определения того, какая рабочая группа назначается.  Весовой коэффициент выглядит следующим образом.

|Параметр классификатора |Вес   |
|---------------------|---------|
|ИМЯ УЧАСТНИКА:ПОЛЬЗОВАТЕЛЬ      |64       |
|MEMBERNAME:РОЛЬ      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Параметр `membername` является обязательным.  Однако, если указанное имя участника является пользователем базы данных, а не ролью базы данных, взвешивание для пользователя выше и, таким образом, выбран классификатор.

Если пользователь является членом нескольких ролей, которым назначены разные классы ресурсов или сопоставлены несколько классификаторов, пользователю назначается класс ресурсов наиболее высокого уровня.  Это поведение согласуется с поведением назначения существующего класса ресурсов.

## <a name="system-classifiers"></a>Системные классификаторы

Классификация рабочей нагрузки имеет классификаторы рабочей нагрузки системы. Классификаторы системы отображали существующие членские роли класса ресурсов с распределением ресурсов класса ресурсов с нормальным значением. Системные классификаторы не могут быть удалены. Для просмотра классификаторов систем можно запустить нижеприведенный запрос:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Смешивание заданий класса ресурсов с классификаторами

Системные классификаторы, созданные от вашего имени, обеспечивают простой путь для перехода к классификации рабочей нагрузки. Использование отображения ролей класса ресурса с приоритетом классификации может привести к неправильной классификации при возникновении новых классификаторов с важностью.

Рассмотрим следующий сценарий.

- Существующий хранилище данных имеет пользователя базы данных DBAUser, назначенного роли класса ресурсов largerc. Назначение класса ресурсов было выполнено с sp_addrolemember.
- Хранилище данных теперь обновляется с помощью управления рабочей нагрузкой.
- Для тестирования нового синтаксиса классификации роль базы данных DBARole (членом которой является DBAUser), имеет классификатор, созданный для них отображение их до среднего и высокого значения.
- Когда DBAUser входит в систему и запускает запрос, запрос будет назначен более крупным. Потому что пользователь имеет приоритет над членством в роли.

Чтобы упростить неправильное устранение неполадок, мы рекомендуем удалить отображение ролей класса ресурсов при создании классификаторов рабочей нагрузки.  Приведенный ниже код возвращает существующие членские роли класса ресурсов.  Выполнить [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) для каждого имени участника, возвращенного из соответствующего класса ресурсов.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Дальнейшие действия

- Для получения дополнительной информации о создании классификатора, [см.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
- Ознакомьтесь с тем, как создать классификатор рабочей [нагрузки, создайте классификатор рабочей нагрузки.](quickstart-create-a-workload-classifier-tsql.md)
- Ознакомьтесь со [статьями, настраиваемыми значением рабочей нагрузки,](sql-data-warehouse-how-to-configure-workload-importance.md) а также как [управлять и контролировать управление рабочей нагрузкой.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Запросы и назначенную важность см. в разделе [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
