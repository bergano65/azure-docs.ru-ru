---
title: Администрирование и мониторинг уровня важности рабочей нагрузки
description: Узнайте, как управлять и отслеживать важность уровня запросов в хранилище данных SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692713"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Управление и мониторинг важности рабочей нагрузки в хранилище данных SQL Azure

Управление и отслеживание важности уровня запросов в хранилище данных SQL Azure с помощью DMV и представлений каталога.

## <a name="monitor-importance"></a>Мониторинг важности

Следите за важностью с помощью столбца New важности в динамическом административном представлении [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) .
В приведенном ниже запросе наблюдения показано время и время начала отправки запросов. Просмотрите время отправки и время начала, а также важность, чтобы увидеть, как важно планирование.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Чтобы просмотреть дальнейшие сведения о расписании запросов, используйте представления каталога.

## <a name="manage-importance-with-catalog-views"></a>Управление важностью с помощью представлений каталога

Представление каталога sys. workload_management_workload_classifiers содержит сведения о классификаторах в экземпляре хранилища данных SQL Azure. Чтобы исключить определенные системой классификаторы, которые сопоставляются с классами ресурсов, выполните следующий код:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Представление каталога [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)содержит сведения о параметрах, используемых при создании классификатора.  В приведенном ниже запросе показано, что Ексекрепортсклассифиер был создан в параметре ```membername``` для значений с Ексекутиверепортс:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![результаты запроса](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Чтобы упростить устранение неполадок классификации, рекомендуется удалить сопоставления ролей класса ресурсов при создании классификаторов рабочей нагрузки. Приведенный ниже код возвращает членство в роли существующего класса ресурсов. Запустите sp_droprolemember для каждого ```membername```, возвращенного из соответствующего класса ресурсов.
Ниже приведен пример проверки существования перед удалением классификатора рабочей нагрузки.

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о классификации см. в разделе [классификация рабочей нагрузки](sql-data-warehouse-workload-classification.md).
- Дополнительные сведения о важности см. в статье [важность рабочей нагрузки](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Перейдите к разделу Настройка важности рабочей нагрузки](sql-data-warehouse-how-to-configure-workload-importance.md)
