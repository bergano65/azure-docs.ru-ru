---
title: Настройка рабочей нагрузки важности в хранилище данных SQL Azure | Документация Майкрософт
description: Узнайте, как для установки уровня важности запроса.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7121d2f022f9c9a5bbc02f04955d2857f3ec986f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241235"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Настройка рабочей нагрузки важности в хранилище данных SQL Azure

Задание важности в хранилище данных SQL позволяет повлиять на планирование запросов. Запросы с высокой важностью будет запланировано для выполнения перед запросы с низким важности. Чтобы назначить важность запросов, вам потребуется создать классификатор рабочей нагрузки.

## <a name="create-a-workload-classifier-with-importance"></a>Создание рабочей нагрузки классификатора с важностью

Часто в случае хранилища данных у вас есть пользователей, которым требуется быстро выполнять свои запросы.  Пользователь сможет руководители компании, которые требуется запускать отчеты или пользователь может быть аналитик выполнение нерегламентированного запроса. Вы создадите классификатора рабочей нагрузки для назначения важности для запроса.  В приведенных ниже примерах использовать новый [Создание рабочей нагрузки классификатора](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) синтаксис для создания двух классификаторов.  Имя пользователя может быть одного пользователя или группы. Классификации отдельного пользователя имеют приоритет над классификации роли. Чтобы найти существующие пользователи хранилища данных, выполните следующую команду:

```sql
Select name from sys.sysusers
```

Чтобы создать классификатор рабочей нагрузки, для пользователя, имеющего большую важность выполните:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Для создания классификатора рабочей нагрузки для пользователя под управлением ad-hoc-запросы с низким важностью запуска:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения об управлении рабочей нагрузки, см. в разделе [классификации рабочей нагрузки](sql-data-warehouse-workload-classification.md)
- Дополнительные сведения о важности, см. в разделе [важность рабочих нагрузок](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Перейдите на управление и мониторинг рабочей нагрузки важности ](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
