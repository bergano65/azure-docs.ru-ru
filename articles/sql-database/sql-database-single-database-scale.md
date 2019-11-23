---
title: Scale single database resources
description: В этой статье описано масштабирование вычислительных ресурсов и ресурсов хранилища, предоставляемых отдельной базе данных в Базе данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 8d4917bb8956185e0cb557368fbb0c64343c0ac6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422537"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Масштабирование ресурсов отдельной базы данных в Базе данных SQL Azure

This article describes how to scale the compute and storage resources available for an Azure SQL Database in the provisioned compute tier. Alternatively, the [serverless compute tier](sql-database-serverless.md) provides compute auto-scaling and bills per second for compute used.

## <a name="change-compute-size-vcores-or-dtus"></a>Change compute size (vCores or DTUs)

After initially picking the number of vCores or DTUs, you can scale a single database up or down dynamically based on actual experience using the [Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), the [Azure CLI](/cli/azure/sql/db#az-sql-db-update), or the [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

В следующем видео показано динамическое изменение уровня служб и объема вычислительных ресурсов для увеличения количества доступных единиц DTU отдельной базы данных.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Иногда требуется сжать базу данных, чтобы освободить неиспользуемое пространство. Дополнительные сведения см. в статье об [управлении файловым пространством в Базе данных SQL Azure](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impact of changing service tier or rescaling compute size

Changing the service tier or compute size of mainly involves the service performing the following steps:

1. Create new compute instance for the database  

    A new compute instance is created with the requested service tier and compute size. For some combinations of service tier and compute size changes, a replica of the database must be created in the new compute instance which involves copying data and can strongly influence the overall latency. Regardless, the database remains online during this step, and connections continue to be directed to the database in the original compute instance.

2. Switch routing of connections to new compute instance

    Existing connections to the database in the original compute instance are dropped. Any new connections are established to the database in the new compute instance. For some combinations of service tier and compute size changes, database files are detached and reattached during the switch.  Regardless, the switch can result in a brief service interruption when the database is unavailable generally for less than 30 seconds and often for only a few seconds. If there are long running transactions running when connections are dropped, the duration of this step may take longer in order to recover aborted transactions. [Accelerated Database Recovery](sql-database-accelerated-database-recovery.md) can reduce the impact from aborting long running transactions.

> [!IMPORTANT]
> No data is lost during any step in the workflow. Make sure that you have implemented some [retry logic](sql-database-connectivity-issues.md) in the applications and components that are using Azure SQL Database while the service tier is changed.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latency of changing service tier or rescaling compute size

The estimated latency to change the service tier or rescale the compute size of a single database or elastic pool is parameterized as follows:

|Уровень служб|Basic single database,</br>Standard (S0-S1)|Basic elastic pool,</br>Standard (S2-S12), </br>Hyperscale, </br>General Purpose single database or elastic pool|Premium or Business Critical single database or elastic pool|
|:---|:---|:---|:---|
|**Basic single database,</br> Standard (S0-S1)**|&bull; &nbsp;Constant time latency independent of space used</br>&bull; &nbsp;Typically, less than 5 minutes|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|
|**Basic elastic pool, </br>Standard (S2-S12), </br>Hyperscale, </br>General Purpose single database or elastic pool**|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Constant time latency independent of space used</br>&bull; &nbsp;Typically, less than 5 minutes|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|
|**Premium or Business Critical single database or elastic pool**|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|

> [!TIP]
> To monitor in-progress operations, see: [Manage operations using the SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Manage operations using CLI](/cli/azure/sql/db/op), [Monitor operations using T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) and these two PowerShell commands: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) and [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Cancelling service tier changes or compute rescaling operations

A service tier change or compute rescaling operation can be canceled.

#### <a name="azure-portal"></a>портала Azure

In the database overview blade, navigate to **Notifications** and click on the tile indicating there is an ongoing operation:

![Ongoing operation](media/sql-database-single-database-scale/ongoing-operations.png)

Next, click on the button labeled **Cancel this operation**.

![Cancel ongoing operation](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

From a PowerShell command prompt, set the `$resourceGroupName`, `$serverName`, and `$databaseName`, and then run the following command:

```powershell
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Additional considerations when changing service tier or rescaling compute size

- При переходе к более высокому уровню служб или объему вычислительных ресурсов максимальный размер базы данных не увеличивается, если явно не указать для него большее значение (maxsize).
- При понижении уровня базы данных используемое ею пространство не должно превышать максимальный допустимый размер целевых уровня служб и объема вычислительных ресурсов.
- При понижении уровня служб **Премиум** до уровня **Стандартный** дополнительная плата взимается в следующих случаях: 1) максимальный размер базы данных поддерживается в целевом объеме вычислительных ресурсов; 2) максимальный размер превышает включенный объем хранилища целевого объема вычислительных ресурсов. For example, if a P1 database with a max size of 500 GB is downsized to S3, then an extra storage cost applies since S3 supports a max size of 1 TB and its included storage amount is only 250 GB. Поэтому дополнительный объем хранилища равен 500 – 250 = 250 ГБ. Чтобы узнать о ценах на дополнительное хранилище, ознакомьтесь с [ценами на Базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/). Если фактический объем пространства, которое используется, меньше, чем включенный объем хранилища, то этих дополнительных затрат можно избежать, уменьшив максимальный размер базы данных до включенного объема.
- Если вы повышаете уровень базы данных со включенной [георепликацией](sql-database-geo-replication-portal.md), перед повышением уровня базы данных-источника сначала обновите базы данных-получатели до требуемого уровня служб и объема вычислительных ресурсов (общая рекомендация для оптимальной производительности). Чтобы выполнить обновление до другого выпуска, сначала необходимо обновить базу данных-получатель.
- Если вы понижаете уровень базы данных со включенной [георепликацией](sql-database-geo-replication-portal.md), перед понижением уровня базы данных-получателя сначала понизьте категорию ее базы данных-источника до требуемого уровня служб и объема вычислительных ресурсов (общая рекомендация для оптимальной производительности). Чтобы понизить уровень до другого выпуска, сначала необходимо понизить уровень базы данных-получателя.
- Предложения службы восстановления отличаются для разных уровней служб. При понижении до уровня **Базовый** уменьшится период хранения резервной копии. Ознакомьтесь со статьей [Подробнее об автоматически создаваемых резервных копиях в Базе данных SQL](sql-database-automated-backups.md).
- Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.

### <a name="billing-during-compute-rescaling"></a>Billing during compute rescaling

Плата взимается за каждый час существования базы данных с учетом самого высокого уровня служб и объема вычислительных ресурсов, которые использовались в течение этого часа, даже если база данных использовалась или была активна менее часа. Например, если вы создадите отдельную базу данных и через 5 минут удалите ее, вам будет выставлен счет за 1 час использования базы данных.

## <a name="change-storage-size"></a>Изменение размера хранилища

### <a name="vcore-based-purchasing-model"></a>Модель приобретения на основе виртуальных ядер

- Хранилище может быть расширено до максимально возможного размера с шагом приращения в 1 ГБ. Минимальный настраиваемый размер хранилища данных составляет 5 ГБ.
- Хранилище для отдельной базы данных можно подготовить, увеличив или уменьшив ее максимальный размер с помощью [портала Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update) или [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- База данных SQL автоматически выделяет 30 % доступного места для хранения файлов журнала и 32 ГБ на виртуальное ядро для базы данных tempdb, но при этом их суммарный объем не должен превышать 384 ГБ. База данных tempdb находится на подключенном диске SSD на всех уровнях служб.
- Стоимость хранилища для отдельной базы данных равна суммарному объему хранилища данных и журналов, умноженному на цену единицы хранения для этого уровня служб. Стоимость базы данных tempdb включена в стоимость виртуального ядра. Сведения о цене на дополнительное хранилище см. на [странице цен на Базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Иногда требуется сжать базу данных, чтобы освободить неиспользуемое пространство. Дополнительные сведения см. в статье об [управлении файловым пространством в Базе данных SQL Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Модель приобретения на основе единиц DTU

- Цена DTU для отдельной базы данных включает в себя определенный объем хранилища, не требующий дополнительной платы. Дополнительный объем хранилища, сверх включенного, можно подготовить за дополнительную плату в пределах максимального допустимого размера с шагом в 250 ГБ при объеме хранилища до 1 ТБ и с шагом в 256 ГБ — при объеме более 1 ТБ. Сведения о включенном объеме хранилища и ограничениях максимального размера см. в разделе [Отдельная база данных: размеры хранилища и объемы вычислительных ресурсов](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Дополнительное хранилище для отдельной базы данных можно подготовить, увеличив ее максимальный размер с помощью портала Azure, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update) или [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Стоимость дополнительного хранилища для отдельной базы данных равна объему хранилища, умноженному на цену единицы хранения этого хранилища для уровня служб. Сведения о цене на дополнительное хранилище см. на [странице цен на Базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Иногда требуется сжать базу данных, чтобы освободить неиспользуемое пространство. Дополнительные сведения см. в статье об [управлении файловым пространством в Базе данных SQL Azure](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 and P15 constraints when max size greater than 1 TB

More than 1 TB of storage in the Premium tier is currently available in all regions except: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regions, and US Government Central. В этих регионах максимальный объем хранилища класса Premium ограничен 1 ТБ. Ниже приведены рекомендации и ограничения для баз данных P11 и P15 с максимальным размером, превышающим 1 ТБ.

- If the max size for a P11 or P15 database was ever set to a value greater than 1 TB, then can it only be restored or copied to a P11 or P15 database.  Subsequently, the database can be rescaled to a different compute size provided the amount of space allocated at the time of the rescaling operation does not exceed max size limits of the new compute size.
- Для сценариев активной георепликации:
  - Настройка отношения георепликации. Если используется база данных-источник P11 или P15, уровень баз данных-получателей также должен быть P11 или P15. Базы данных с более низким объемом вычислительных ресурсов отклоняются, так как они не поддерживают максимальный размер больше 1 ТБ.
  - Обновление базы данных-источника в отношениях георепликации. При изменении максимального размера и указании значения больше 1 ТБ в базе данных-источнике такие же изменения произойдут в базе данных-получателе. Чтобы изменения в базе данных-источнике вступили в силу, оба обновления должны быть успешными. При этом применяются ограничения по регионам для максимального размера больше 1 ТБ. Если база данных-получатель находится в регионе, не поддерживающем максимальный размер больше 1 TB, то база данных-источник не обновляется.
- Использование службы импорта и экспорта для загрузки баз данных P11 и P15 с максимальным размером больше 1 ТБ не поддерживается. Для [импорта](sql-database-import.md) и [экспорта](sql-database-export.md) данных используйте файл SqlPackage.exe.

## <a name="next-steps"></a>Дальнейшие действия

Сведения об общих ограничениях ресурсов см. в статьях [Ограничения ресурсов для отдельной базы данных в Базе данных SQL Azure при использовании модели приобретения на основе виртуальных ядер (предварительная версия)](sql-database-vcore-resource-limits-single-databases.md) и [SQL Database DTU-based resource limits - elastic pools](sql-database-dtu-resource-limits-single-databases.md) (Ограничения ресурсов для эластичных пулов в Базе данных SQL при использовании модели приобретения на основе единиц DTU).
