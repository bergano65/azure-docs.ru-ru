---
title: Администрирование и мониторинг уровня важности рабочей нагрузки
description: Узнайте, как управлять и контролировать значение уровня запросов в Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fdac8ebc56291292559f6718de5f0092c7d8e063
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350471"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Управление и мониторинг важности рабочей нагрузки в Azure Synapse Analytics

Управление и мониторинг важности уровня запросов s'L Analytics в Azure Synapse с помощью DMV и представлений каталогов.

## <a name="monitor-importance"></a>Важность мониторинга

Мониторинг важности с использованием новой колонки важности в [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) динамическом представлении управления.
Ниже мониторинговый запрос показывает время отправки и время начала запросов. Просмотрите время отправки и время начала, а также важно, чтобы увидеть, как важность влияет на планирование.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Чтобы углубиться в расписание запросов, используйте представления каталога.

## <a name="manage-importance-with-catalog-views"></a>Управление значением с представлениями каталога

Представление каталога sys.workload_management_workload_classifiers содержит информацию о классификаторах. Чтобы исключить системные классификаторы, которые накартеили для классов ресурсов, выполнить следующий код:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Представление каталога, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), содержит информацию о параметрах, используемых при создании классификатора.  Ниже приведенный запрос показывает, что ExecReportsClassifier был создан по параметру ```membername``` значений с ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![результаты запроса](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Чтобы упростить неправильное устранение неполадок, мы рекомендуем удалить отображение ролей класса ресурсов при создании классификаторов рабочей нагрузки. Приведенный ниже код возвращает существующие членские роли класса ресурсов. Выполнить sp_droprolemember ```membername``` для каждого возвращенного из соответствующего класса ресурсов.
Ниже приведен пример проверки существования перед падением классификатора рабочей нагрузки:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Дальнейшие действия
- Для получения дополнительной информации о классификации [см.](sql-data-warehouse-workload-classification.md)
- Для получения дополнительной информации о важности [см.](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Перейти к настройке важности рабочей нагрузки](sql-data-warehouse-how-to-configure-workload-importance.md)
