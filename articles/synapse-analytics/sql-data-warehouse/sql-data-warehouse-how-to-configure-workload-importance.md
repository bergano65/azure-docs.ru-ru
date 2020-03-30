---
title: Настройка уровня важности рабочей нагрузки
description: Узнайте, как установить значение уровня запроса в Azure Synapse Analytics.
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
ms.openlocfilehash: 85fa6a989ae1a6214392914ffd46cd12af27a8d2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350480"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Настройка важности рабочей нагрузки в аналитике Azure Synapse

Заяц значение в S'L Analytics для Azure Synapse позволяет влиять на планирование запросов. Запросы с более высокой важностью будут запланированы для выполнения запросов с меньшим значением. Чтобы присвоить значение запросам, необходимо создать классификатор рабочей нагрузки.

## <a name="create-a-workload-classifier-with-importance"></a>Создание классификатора рабочей нагрузки с важностью

Часто в сценарии хранилища данных у вас есть пользователи, которым нужно, чтобы их запросы запускались быстро.  Пользователь может быть руководителем компании, которым необходимо запускать отчеты, или он может быть аналитиком, запускаемым запросом adhoc. Создается классификатор рабочей нагрузки для придания значения запросу.  Приведенные ниже примеры используют новый [синтаксис классификатора рабочей нагрузки](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) для создания двух классификаторов.  Членом может быть один пользователь или группа. Индивидуальные классификации пользователей имеют приоритет над классификациями ролей. Чтобы найти существующих пользователей хранилища данных, запустите:

```sql
Select name from sys.sysusers
```

Для создания классификатора рабочей нагрузки для пользователя с более высоким значением:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Для создания классификатора рабочей нагрузки для пользователя, запускаемого запросами adhoc с меньшим значением запуска:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Next Steps
- Для получения дополнительной информации об управлении рабочей нагрузкой [см.](sql-data-warehouse-workload-classification.md)
- Для получения дополнительной информации о важности [см.](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Перейти к управлению и мониторингу важности рабочей нагрузки](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
