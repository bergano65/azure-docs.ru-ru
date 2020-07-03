---
title: Настройка уровня важности рабочей нагрузки
description: Узнайте, как задать важность уровня запроса в Azure синапсе Analytics.
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
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633367"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Настройка важности рабочей нагрузки в Azure синапсе Analytics

Настройка важности в синапсе SQL для Azure синапсе позволяет повлиять на планирование запросов. Запросы с более высоким уровнем важности будут выполняться перед запросами с более низкой важностью. Чтобы назначить важность запросам, необходимо создать классификатор рабочей нагрузки.

## <a name="create-a-workload-classifier-with-importance"></a>Создание классификатора рабочей нагрузки с важностью

Часто в сценарии хранилища данных есть пользователи, которым нужны запросы для быстрого запуска.  Пользователь может быть руководителем компании, которой требуется запускать отчеты, или пользователь может быть аналитиком, выполняющим регламентированный запрос. Для назначения важности запроса необходимо создать классификатор рабочей нагрузки.  В приведенных ниже примерах используется новый синтаксис [классификатора создания рабочей нагрузки](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) для создания двух классификаторов. `Membername`может быть одним пользователем или группой. Классификации отдельных пользователей имеют приоритет над классификациями ролей. Чтобы найти существующих пользователей хранилища данных, выполните:

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

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения об управлении рабочими нагрузками см. в разделе [классификация рабочей нагрузки](sql-data-warehouse-workload-classification.md) .
- Дополнительные сведения о важности см. в статье [важность рабочей нагрузки](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Выберите Управление и отслеживание важности рабочей нагрузки](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
