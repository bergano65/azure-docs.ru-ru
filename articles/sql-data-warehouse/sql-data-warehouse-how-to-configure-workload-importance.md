---
title: Настройка уровня важности рабочей нагрузки
description: Узнайте, как установить важность уровня запроса.
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
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692690"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Настройка важности рабочей нагрузки в хранилище данных SQL Azure

Настройка важности в хранилище данных SQL позволяет повлиять на планирование запросов. Запросы с более высоким уровнем важности будут выполняться перед запросами с более низкой важностью. Чтобы назначить важность запросам, необходимо создать классификатор рабочей нагрузки.

## <a name="create-a-workload-classifier-with-importance"></a>Создание классификатора рабочей нагрузки с важностью

Часто в сценарии хранилища данных есть пользователи, которым нужны запросы для быстрого запуска.  Пользователь может быть руководителем компании, которой требуется запускать отчеты, или пользователь может быть аналитиком, выполняющим регламентированный запрос. Для назначения важности запроса необходимо создать классификатор рабочей нагрузки.  В приведенных ниже примерах используется новый синтаксис [классификатора создания рабочей нагрузки](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) для создания двух классификаторов.  MemberName может быть одним пользователем или группой. Классификации отдельных пользователей имеют приоритет над классификациями ролей. Чтобы найти существующих пользователей хранилища данных, выполните:

```sql
Select name from sys.sysusers
```

Чтобы создать классификатор рабочей нагрузки, выполните следующие действия для пользователя с более высоким уровнем важности:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Чтобы создать классификатор рабочей нагрузки для пользователя, выполняющего нерегламентированные запросы с более низким уровнем важности, выполните следующие действия:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об управлении рабочими нагрузками см. в разделе [классификация рабочей нагрузки](sql-data-warehouse-workload-classification.md) .
- Дополнительные сведения о важности см. в статье [важность рабочей нагрузки](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Выберите Управление и отслеживание важности рабочей нагрузки](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
