---
title: Настройка уровня важности рабочей нагрузки
description: Узнайте, как задать важность уровня запроса в Azure синапсе Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196804"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Настройка важности рабочей нагрузки в Azure синапсе Analytics

Настройка важности в SQL Analytics для Azure синапсе позволяет повлиять на планирование запросов. Запросы с более высоким уровнем важности будут выполняться перед запросами с более низкой важностью. Чтобы назначить важность запросам, необходимо создать классификатор рабочей нагрузки.

## <a name="create-a-workload-classifier-with-importance"></a>Создание классификатора рабочей нагрузки с важностью

Часто в сценарии хранилища данных есть пользователи, которым нужны запросы для быстрого запуска.  Пользователь может быть руководителем компании, которой требуется запускать отчеты, или пользователь может быть аналитиком, выполняющим регламентированный запрос. Для назначения важности запроса необходимо создать классификатор рабочей нагрузки.  В приведенных ниже примерах используется новый синтаксис [классификатора создания рабочей нагрузки](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) для создания двух классификаторов.  MemberName может быть одним пользователем или группой. Классификации отдельных пользователей имеют приоритет над классификациями ролей. Чтобы найти существующих пользователей хранилища данных, выполните:

```sql
Select name from sys.sysusers
```

Чтобы создать классификатор рабочей нагрузки, выполните следующие действия для пользователя с более высоким уровнем важности:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Чтобы создать классификатор рабочей нагрузки для пользователя, выполняющего нерегламентированные запросы с более низким уровнем важности, выполните следующие действия:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Next Steps
- Дополнительные сведения об управлении рабочими нагрузками см. в разделе [классификация рабочей нагрузки](sql-data-warehouse-workload-classification.md) .
- Дополнительные сведения о важности см. в статье [важность рабочей нагрузки](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Выберите Управление и отслеживание важности рабочей нагрузки](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
