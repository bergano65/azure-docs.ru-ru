---
title: Обновление до последнего поколения
description: Обновление пула Azure Synapse Analytics s-L до последнего поколения аппаратного обеспечения и архитектуры хранения данных Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 637e377e469eeb1a82b6c0ad3a845d94ac09c7db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351198"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Оптимизация производительности за счет обновления пула Azure Synapse Analytics S'L

Обновление пула S'L до последнего поколения аппаратного обеспечения и архитектуры хранения данных Azure.

## <a name="why-upgrade"></a>Зачем выполнять обновление

Теперь можно плавно перейти на уровень оптимизированного уровня «Компьютер Gen2» на портале Azure для [поддерживаемых регионов.](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) Если ваш регион не поддерживает самостоятельное обновление, можно перейти на поддерживаемый регион или дождаться, когда функция самостоятельного обновления станет доступна в вашем регионе. Выполните обновление сейчас, чтобы воспользоваться преимуществами последнего поколения оборудования Azure и усовершенствованной архитектуры хранилища, включая повышенную производительность, масштабируемость и неограниченное хранение по столбцам. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Применяется к

Это обновление относится к пулам вычислительного оптимизированного уровня Gen1 в [поддерживаемых регионах.](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)

## <a name="before-you-begin"></a>Перед началом

1. Проверьте, поддерживает ли ваш [регион](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) перенос хранилища 1-го поколения в хранилище 2-го поколения. Обратите внимание на даты автоматического переноса. Чтобы избежать конфликтов с автоматизированным процессом, запланируйте ручной перенос на дату, предшествующую дате запуска автоматизированного процесса.
2. Если вы находитесь в регионе, который еще не поддерживается, регулярно проверяйте, не был ли ваш регион добавлен в список поддерживаемых, или выполните [обновление с помощью восстановления](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) в поддерживаемый регион.
3. Если ваш регион не поддерживается, выполните [обновление на портале Azure](#upgrade-in-a-supported-region-using-the-azure-portal).
4. **Выберите предлагаемый уровень производительности** для пула S'L на основе текущего уровня производительности на уровне Compute Optimized Gen1, используя отображение ниже:

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

> [!Note]
> Предлагаемые уровни производительности не гарантируют прямое преобразование. Например, мы рекомендуем перейти с DW600 на DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Обновление в поддерживаемом регионе с помощью портала Azure

## <a name="before-you-begin"></a>Перед началом

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Миграция с Gen1 в Gen2 через портал Azure является постоянной. Существует не процесс возвращения в Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

1. Если переутим пул вычислительного оптимизированного уровня Gen1, который будет обновлен, [возобновится, возобновите пул S'L.](pause-and-resume-compute-portal.md)

   > [!NOTE]
   > Для перехода на Gen2 должен быть запущен пул S'L.

2. Будьте готовы к непродолжительному простою. 

3. Определите все ссылки на код, связанные с уровнями производительности 1-го поколения "Оптимизировано для вычислений" и измените их на эквивалентный уровень производительности 2-го поколения "Оптимизировано для вычислений" Ниже представлены два примера, в которых нужно обновить ссылки на код перед обновлением:

   Исходная команда PowerShell 1-го поколения:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Изменено на:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
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

1. Перейдите в свой оптимизированный пул Gen1 СЗЛ на портале Azure. Если переутим пул вычислительного оптимизированного уровня Gen1, который будет обновлен, [возобновится, возобновите пул S'L.](pause-and-resume-compute-portal.md) 
2. Выберите обновление до карты **Gen2** под вкладкой Задачи: ![Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)
    
    > [!NOTE]
    > Если карта **Повысить до Gen2** не отображается на вкладке "Задачи", для типа вашей подписки действуют ограничения в текущем регионе.
    > [Отправьте запрос в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md) на добавление вашей подписки в список разрешений.

3. Перед обновлением убедитесь, что рабочая нагрузка завершена и приостановлена. Вы будете испытывать простои в течение нескольких минут, прежде чем ваш пул S'L снова в сети, как вычислительный оптимизированный Gen2 уровня S'L бассейн. Выберите **Обновить**.

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Наблюдайте за процессом обновления**, проверяя состояние на портале Azure.

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   Первым шагом процесса обновления является масштабирование ("Обновление — автономный режим"), при котором все сеансы будут прерваны, а соединение разорвано. 

   Второй шаг процесса обновления — перенос данных ("Обновление — в сети"). Перенос данных является малозаметным фоновым сетевым процессом. Он медленно перемещает данные столбцов из старой архитектуры службы хранилища в новую архитектуру службы хранилища, использующую локальный кэш SSD. В течение этого времени ваш пул S'L будет находиться в режиме онлайн для запросов и загрузки. При этом все данные будут доступны для запросов, вне зависимости от того, были ли они перенесены. Перенос данных происходит с разной скоростью в зависимости от их размера, уровня производительности и количества сегментов columnstore. 

5. **Дополнительная рекомендация:** После завершения операции масштабирования можно ускорить процесс обработки данных. Вы можете сразу инициировать перемещение данных, выполнив инструкцию [Alter Index rebuild](sql-data-warehouse-tables-index.md) для всех основных таблиц columnstore, которые вы сможете запрашивать при наличии более высокого целевого уровня обслуживания и оптимального класса ресурсов. Эта операция является **автономной** по сравнению с малозаметным фоновым процессом, который может потребовать несколько часов в зависимости от количества и размеров таблиц. Тем не менее после завершения этой операции перенос данных будет происходить намного быстрее благодаря новой улучшенной архитектуре хранилища с высококачественными группами строк.
 
> [!NOTE]
> Операция "Alter Index rebuild" — это автономная операция. Таблицы будут недоступны до завершения повторной сборки.

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Обновление из географического региона Azure с помощью восстановления на портале Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Создание определяемой пользователем точки восстановления с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Перейдите к пулу S'L, для которого необходимо создать точку восстановления.

3. В верхней области колонки "Обзор" выберите **Новая точка восстановления**.

    ![Новая точка восстановления](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Укажите имя точки восстановления.

    ![Имя точки восстановления](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Восстановление активной или приостановленной базы данных с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к пулу S'L, который вы хотите восстановить.
3. В верхней области колонки "Обзор" выберите **Восстановление**.

    ![ Обзор восстановления](./media/upgrade-to-latest-generation/restoring_0.png)

4. Выберите либо **автоматические точки восстановления,** либо **определенные пользователем точки восстановления.** Для точек восстановления, определяемых пользователем, **выберите определяемую пользователем точку восстановления** или **создайте новую точку восстановления, определяемую пользователем.** Для сервера выберите **Создать новый** и выбрать сервер в поддерживаемом Gen2 географическом регионе. 

    ![Точки автоматического восстановления](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Восстановление из географического региона Azure с помощью PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Для восстановления базы данных используйте cmdlet [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Можно выполнить геовосстановление в хранилище 2-го поколения! Для этого в качестве необязательного параметра укажите имя ServiceObjectiveName 2-го поколения (например, DW1000**c**).

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую базу данных, которую нужно восстановить.
4. Получите базу данных, которую требуется восстановить.
5. Создайте запрос на восстановление для базы данных, указав ServiceObjectiveName 2-го поколения.
6. Проверьте состояние геовосстановленной базы данных.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Чтобы настроить базу данных после восстановления, см. раздел [Настройка базы данных после восстановления](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

Восстановленная база данных будет поддерживать прозрачное шифрование данных, если исходная база данных поддерживает прозрачное шифрование данных.


Если у вас возникли какие-либо проблемы с пулом S'L, создайте [запрос на поддержку](sql-data-warehouse-get-started-create-support-ticket.md) и ссылку "Gen2 обновление" в качестве возможной причины.

## <a name="next-steps"></a>Дальнейшие действия

Ваш обновленный пул S'L находится в сети. Чтобы воспользоваться преимуществами новой, усовершенствованной архитектуры, см. [Классы ресурсов для управления рабочей нагрузкой](resource-classes-for-workload-management.md).
