---
title: Управление и наблюдение за важной рабочей нагрузки в хранилище данных SQL Azure | Документация Майкрософт
description: Узнайте, как управлять и отслеживать запрос уровня важности
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 8d6cd80f569be58b520a53faa0d4bda8f86af104
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003496"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Управление и наблюдение за важной рабочей нагрузки в хранилище данных SQL Azure

Управление и мониторинг уровня важности запроса в хранилище данных SQL Azure, с помощью представления каталога и динамические административные представления.

## <a name="monitor-importance"></a>Монитор важности

Мониторинг важности, с помощью нового столбца важности [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) динамическое административное представление.
Ниже мониторинг запросов отображается время отправки и время начала для запросов. Просмотрите время отправки и время, а также важности, чтобы отобразить как важность повлияло на планирование начала.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Чтобы продвиньтесь в том, как запросы выполняется расписание, используйте представления каталога.

## <a name="manage-importance-with-catalog-views"></a>Управление важности, с помощью представления каталога

Представление каталога sys.workload_management_workload_classifiers содержит сведения о классификаторов в вашем экземпляре хранилища данных SQL Azure. Чтобы исключить системный классификаторы, сопоставляются с классами ресурсов выполните следующий код:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Представление каталога [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), содержащий сведения о параметрах, используемых при создании классификатора.  Ниже показан запрос, на которые был создан ExecReportsClassifier ```membername``` параметр для значений с помощью ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Результаты запроса](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Для упрощения устранения неполадок неправильной классификации, мы рекомендуем удалить сопоставления ролей класс ресурсов, при создании классификаторов рабочей нагрузки. Приведенный ниже код возвращает существующий ресурс класса членства в роли. Запустите sp_droprolemember для всех ```membername``` возвращается из соответствующего класса ресурсов.
Ниже приведен пример проверки наличия перед удалением классификатора рабочей нагрузки:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о классификации, см. в разделе [классификации рабочей нагрузки](sql-data-warehouse-workload-classification.md).
- Дополнительные сведения о важности, см. в разделе [важность рабочих нагрузок](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Перейти к настройке важность рабочих нагрузок ](sql-data-warehouse-how-to-configure-workload-importance.md)
