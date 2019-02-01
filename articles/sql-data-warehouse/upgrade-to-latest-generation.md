---
title: Обновление до последнего поколения хранилища данных SQL Azure | Документация Майкрософт
description: Обновление хранилища данных SQL Azure до последнего поколения аппаратного обеспечения и архитектуры службы хранилища Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 173846e4828228bdc51fc42858e0c6c9b00cafd6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242796"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Оптимизация производительности путем обновления хранилища данных SQL
Обновление хранилища данных SQL Azure до последнего поколения аппаратного обеспечения и архитектуры службы хранилища Azure.

## <a name="why-upgrade"></a>Причины, по которым стоит установить обновление
Сейчас вы можете легко перейти на уровень хранилища данных SQL 2-го поколения "Оптимизировано для вычислений" на портале Azure. При наличии хранилища данных 1-го поколения уровня "Оптимизировано для вычислений" рекомендуется обновление. После обновления можно будет использовать последнее поколение оборудования Azure и улучшенную архитектуру службы хранилища Azure. Это дает возможность получить повышенную производительность, простую масштабируемость и неограниченное хранение данных в столбцах. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Применяется к
Это обновление применяется к хранилищам данных 1-го поколения уровня "Оптимизировано для вычислений".

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Перед началом работы
> [!NOTE]
> Если имеющееся у вас хранилище данных уровня 1-го поколения "Оптимизировано для вычислений" находится не в том регионе, где доступен уровень 2-го поколения "Оптимизировано для вычислений", с помощью PowerShell вы можете [выполнить георепликацию](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) в регион с поддержкой этого уровня.
>
> 

1. Если работа хранилища данных при обновлении с уровня 1-го поколения "Оптимизировано для вычислений" приостановлена, [возобновите ее](pause-and-resume-compute-portal.md).

2. Будьте готовы к непродолжительному простою. 

3. Определите все ссылки на код, связанные с уровнями производительности 1-го поколения "Оптимизировано для вычислений" и измените их на эквивалентный уровень производительности 2-го поколения "Оптимизировано для вычислений" Ниже представлены два примера, в которых нужно обновить ссылки на код перед обновлением:

   Исходная команда PowerShell 1-го поколения:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Изменено на:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" изменено на -RequestedServiceObjectiveName "DW300**c**"
   >

   Исходная команда T-SQL 1-го поколения:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Изменено на:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
    > [!NOTE] 
    > SERVICE_OBJECTIVE = 'DW300' изменено на SERVICE_OBJECTIVE = 'DW300**c**'



## <a name="start-the-upgrade"></a>Начало обновления

1. На портале Azure перейдите к хранилищу данных 1-го поколения "Оптимизировано для вычислений", а затем на вкладке "Задачи" щелкните карту **Обновление до 2-го поколения**:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Если карта **Повысить до Gen2** не отображается на вкладке "Задачи", для типа вашей подписки действуют ограничения в текущем регионе.
    > [Отправьте запрос в службу поддержки](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) на добавление вашей подписки в список разрешений.

2. На уровне 1-го поколения "Оптимизировано для вычислений" **выберите предлагаемый по умолчанию уровень производительности** для хранилища данных на основе текущего уровня производительности, используя представленное ниже сопоставление.

   | Уровень 1-го поколения "Оптимизировано для вычислений" | Уровень 2-го поколения "Оптимизировано для вычислений" |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

3. Перед обновлением убедитесь, что рабочая нагрузка завершена и приостановлена. Несколько минут ресурсы будут недоступны, прежде чем хранилище данных опять подключится в качестве хранилища данных уровня 2-го поколения "Оптимизировано для вычислений". Щелкните **Обновить**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Наблюдайте за процессом обновления**, проверяя состояние на портале Azure.

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Первым шагом процесса обновления является масштабирование ("Обновление — автономный режим"), при котором все сеансы будут прерваны, а соединение разорвано. 

   Второй шаг процесса обновления — перенос данных ("Обновление — в сети"). Перенос данных является малозаметным фоновым сетевым процессом, который медленно перемещает данные столбцов из старой архитектуры службы хранилища в новую архитектуру службы хранилища, использующую локальный кэш SSD. В течение этого периода хранилище данных будет подключено к сети и доступно для запросов и загрузки данных. При этом все данные будут доступны для запросов, вне зависимости от того, были ли они перенесены. Перенос данных происходит с разной скоростью в зависимости от их размера, уровня производительности и количества сегментов columnstore. 

5. **Дополнительная рекомендация**. Для ускорения фонового процесса миграции данных вы можете сразу инициировать перемещение данных, выполнив инструкцию [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) для всех основных таблиц columnstore, которые вы сможете запрашивать при наличии широких возможностей единого выхода и оптимального класса ресурсов. Эта операция проводится **в автономном режиме** в отличие от малозаметного фонового процесса, выполнение которого может длиться несколько часов в зависимости от количества и размера таблиц. Но перенос данных будет происходить намного быстрее, после чего вы сможете использовать все преимущества новой улучшенной архитектуры службы хранилища после завершения работы с группами строк высокого качества. 

Следующий запрос формирует необходимые команды Alter Index Rebuild для ускорения процесса переноса данных.

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>Дополнительная информация
Ваше хранилище данных подключено к сети. Чтобы воспользоваться преимуществами новой, усовершенствованной архитектуры, см. [Классы ресурсов для управления рабочей нагрузкой](resource-classes-for-workload-management.md).
