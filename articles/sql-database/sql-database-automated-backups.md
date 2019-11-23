---
title: automatic, geo-redundant backups
description: База данных SQL автоматически создает локальную резервную копию базы данных каждые пять минут и использует геоизбыточное хранилище Azure с доступом на чтение для обеспечения геоизбыточности.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: 77442eda6c8b2aae71c5d647127ead9f851ec485
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421417"
---
# <a name="automated-backups"></a>Автоматически создаваемые резервные копии

SQL Database automatically creates the database backups that are kept between 7 and 35 days, and uses Azure [read-access geo-redundant storage (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) to ensure that they are preserved even if the data center is unavailable. These backups are created automatically. Резервные копии базы данных являются важной частью любой стратегии непрерывности бизнес-процессов и аварийного восстановления, так как они защищают данные от случайного повреждения или удаления. If your security rules require that your backups are available for an extended period of time (up to 10 years), you can configure a [long-term retention](sql-database-long-term-retention.md) on Singleton databases and Elastic pools.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Резервная копия базы данных SQL

SQL Database uses SQL Server technology to create [full backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) every week, [differential backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) every 12 hours, and [transaction log backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) every 5-10 minutes. The backups are stored in [RA-GRS storage blobs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) that are replicated to a [paired data center](../best-practices-availability-paired-regions.md) for protection against a data center outage. При восстановлении базы данных служба сама определяет, какие резервные копии (полные, разностные или резервные копии журналов транзакций) следует восстановить.

Эти резервные копии позволяют выполнить следующие операции:

- **Restore an existing database to a point-in-time in the past** within the retention period using the Azure portal, Azure PowerShell, Azure CLI, or REST API. In Single database and Elastic pools, this operation will create a new database in the same server as the original database. In Managed Instance, this operation can create a copy of the database or same or different Managed Instance under the same subscription.
  - **[Change Backup Retention Period](#how-to-change-the-pitr-backup-retention-period)** between 7 to 35 days to configure your backup policy.
  - **Change long-term retention policy up to 10 years** on Single Database and Elastic Pools using [the Azure portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell).
- **Restore a deleted database to the time it was deleted** or anytime within the retention period. The deleted database can only be restored in the same logical server or Managed Instance where the original database was created.
- **Restore a database to another geographical region**. Геовосстановление позволяет выполнить восстановление после сбоя в регионе при отсутствии доступа к серверу и базе данных. В результате создается база данных на любом существующем сервере в любой точке мира.
- **Restore a database from a specific long-term backup** on Single Database or Elastic Pool if the database has been configured with a long-term retention policy (LTR). LTR allows you to restore an old version of the database using [the Azure portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) to satisfy a compliance request or to run an old version of the application. Дополнительные сведения см. в статье [Storing Azure SQL Database Backups for up to 10 years](sql-database-long-term-retention.md) (Хранение резервных копий баз данных SQL Azure до 10 лет).
- Инструкции по восстановлению см. в статье о [восстановлении базы данных из резервной копии](sql-database-recovery-using-backups.md).

> [!NOTE]
> В службе хранилища Azure термин *репликация* обозначает копирование файлов из одного расположения в другое. *Репликация базы данных SQL* означает хранение нескольких баз данных-получателей, синхронизированных с основной базой данных.

You can try some of these operations using the following examples:

| | Портал Azure | Azure PowerShell |
|---|---|---|
| Change backup retention | [Single Database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Управляемый экземпляр](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Управляемый экземпляр](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Change Long-term backup retention | [Отдельная база данных](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance - N/A  | [Single Database](sql-database-long-term-backup-retention-configure.md)<br/>Managed Instance - N/A  |
| Restore database from point-in-time | [Отдельная база данных](sql-database-recovery-using-backups.md#point-in-time-restore) | [Отдельная база данных](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Управляемый экземпляр](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Восстановление удаленной базы данных | [Отдельная база данных](sql-database-recovery-using-backups.md) | [Отдельная база данных](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Управляемый экземпляр](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restore database from Azure Blob Storage | Single database - N/A <br/>Managed Instance - N/A  | Single database - N/A <br/>[Управляемый экземпляр](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Срок хранения резервных копий

All Azure SQL databases (single, pooled, and managed instance databases) have a default backup retention period of  **seven** days. You can [change backup retention period up to 35 days](#how-to-change-the-pitr-backup-retention-period).

При удалении базы данных резервные копии для базы данных SQL будут храниться точно так же, как и для базы данных в Интернете. Например, если вы удалите базу данных уровня "Базовый" с периодом хранения семь дней, резервная копия, хранящаяся четыре дня, будет храниться еще три дня.

Если вам нужно сохранить резервные копии на более долгий срок по сравнению с максимальным периодом хранения, вы можете изменить свойства резервной копии, добавив в базу данных один или несколько периодов долгосрочного хранения. Дополнительные сведения см. в статье [Storing Azure SQL Database Backups for up to 10 years](sql-database-long-term-retention.md) (Хранение резервных копий баз данных SQL Azure до 10 лет).

> [!IMPORTANT]
> Если вы удаляете сервер SQL Azure, на котором размещены базы данных SQL, то все эластичные пулы и базы данных, находящиеся на этом сервере, также будут удалены без возможности восстановления. Удаленный сервер восстановить невозможно. Но если вы настроили долгосрочное хранение, резервные копии баз данных с LTR не будут удалены, и вы сможете восстановить эти базы данных.

## <a name="how-often-do-backups-happen"></a>Частота создания резервных копий

### <a name="backups-for-point-in-time-restore"></a>Резервные копии для восстановления на момент времени

База данных SQL поддерживает самообслуживание для восстановления на момент времени (PITR) путем автоматического создания полной резервной копии, разностных резервных копий и резервных копий журналов транзакций. Полные резервные копии базы данных создаются каждую неделю, разностные резервные копии — как правило, каждые 12 часов, а резервные копии журналов транзакций создаются каждые 5 – 10 минут. Периодичность архивации зависит от объема вычислительных ресурсов и числа действий, производимых с базой данных. Первое полное резервное копирование планируется сразу после создания базы данных. Обычно оно занимает не более 30 минут, но для базы данных большого размера может потребоваться больше времени. Например, начальное резервное копирование будет выполняться дольше для восстановленной базы данных или копии базы данных. После первого полного резервного копирования все последующие операции резервного копирования планируются автоматически и управляются без участия пользователя в фоновом режиме. Точное время создания всех копий базы данных определяет служба Базы данных SQL с учетом распределения общей рабочей нагрузки в системе. You cannot change or disable the backup jobs. 

Резервные копии PITR также обладают географической избыточностью и защищены с помощью [репликации службы хранилища Azure между регионами](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Дополнительную информацию см. в статье [Восстановление до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="backups-for-long-term-retention"></a>Резервные копии для долгосрочного хранения

Изолированные базы данных и базы данных в пуле предоставляют возможность настройки долгосрочного хранения (LTR) полных резервных копий до 10 лет в хранилище BLOB-объектов Azure. Если политика LTR включена, еженедельные полные резервные копии автоматически копируются в другой контейнер геоизбыточного хранилища с доступом на чтение. Чтобы удовлетворять различные требования к соответствию, вы можете выбрать разные периоды хранения для резервных копий, создаваемых еженедельно, ежемесячно или ежегодно. Использование хранилища зависит от частоты резервного копирования и периода хранения. Вы можете использовать [калькулятор цен LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database), чтобы определить стоимость долгосрочного хранения резервных копий.

Как и резервные копии PITR, резервные копии LTR также обладают географической избыточностью и защищены с помощью [репликации службы хранилища Azure между регионами](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Дополнительные сведения см. в разделе [Долгосрочное хранение резервных копий](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Стоимость хранилища
For single databases and managed instances, a minimum backup storage amount equal to 100% of database size is provided at no extra charge. For elastic pools, a minimum backup storage amount equal to 100% of the allocated data storage for the pool is provided at no extra charge. Плата за дополнительное пространство хранилища резервных копий будет взиматься с расчетом ГБ/месяц. This additional consumption will depend on the workload and size of the individual databases.

You can use Azure subscription cost analysis to determine your current spending on backup storage.

![Backup storage cost analysis](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

If you go to your subscription and open Cost Analysis blade, you can select meter subcategory **mi pitr backup storage** to see your current backup cost and charge forecast. You can also include other meter subcategories such as **managed instance general purpose - storage** or **managed instance general purpose - compute gen5** to compare backup storage cost with other cost categories.

> [!Note]
> You can [change retention period to 7 days](#change-pitr-backup-retention-period-using-azure-portal) to reduce the backup storage cost.

Дополнительные сведения о ценах на службу хранилища см. на странице [Обзор цен на хранилище Azure](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>Шифрование резервных копий

Если база данных зашифрована с использованием прозрачного шифрования, резервные копии, включая резервные копии LTR, будут автоматически шифроваться при хранении. Когда для базы данных Azure SQL включено прозрачное шифрование данных, также шифруются резервные копии. Прозрачное шифрование данных включено по умолчанию для всех новых баз данных SQL Azure. Дополнительные сведения о прозрачном шифровании данных см. в статье [Прозрачное шифрование данных в базе данных SQL Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Каким образом корпорация Майкрософт обеспечивает целостность резервной копии?

On an ongoing basis, the Azure SQL Database engineering team automatically tests the restore of automated database backups of databases placed in Logical servers and Elastic pools (this is not available in Managed Instance). Upon point-in-time restore, databases also receive integrity checks using DBCC CHECKDB.

Managed Instance takes automatic initial backup with `CHECKSUM` of the databases restored using native `RESTORE` command or Data Migration Service once the migration is completed.

При обнаружении ошибок в процессе проверки целостности команда разработки получает оповещения. Дополнительные сведения о целостности данных в службе "База данных SQL Azure" см. в [этой записи блога](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Влияние автоматически создаваемых резервных копий на соответствие

При переносе базы данных с уровня служб на основе DTU с периодом хранения PITR по умолчанию 35 дней на уровень служб на основе виртуальных ядер, чтобы не нарушить политику восстановления данных приложения период хранения PITR сохраняется. Если период хранения по умолчанию не соответствует вашим требованиям соответствия, вы можете изменить период хранения PITR с помощью PowerShell или REST API. Дополнительные сведения см. в разделе [Как изменить период хранения резервной копии](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Как изменить период хранения резервной копии PITR

You can change the default PITR backup retention period using the Azure portal, PowerShell, or the REST API. Поддерживаемые значения: 7, 14, 21, 28 и 35 дней. В следующих примерах показано, как установить период хранения PITR на 28 дней.

> [!WARNING]
> If you reduce the current retention period, all existing backups older than the new retention period are no longer available. Если увеличить текущий период хранения, то база данных SQL будет хранить существующие резервные копии до достижения нового установленного периода хранения.

> [!NOTE]
> Эти API-интерфейсы влияют только на период хранения PITR. Если для базы данных настроено LTR, оно не будет затронуто. Дополнительные сведения о том, как изменить период хранения LTR, см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Change PITR backup retention period using Azure portal

To change the PITR backup retention period using the Azure portal, navigate to the server object whose retention period you wish to change within the portal and then select the appropriate option based on which server object you're modifying.

#### <a name="single-database--elastic-poolstabsingle-database"></a>[Отдельная база данных и эластичные пулы](#tab/single-database)

Change of PITR backup retention for single Azure SQL Databases is performed at the server level. Change made at the server level applies to databases on that server. To change PITR for Azure SQL Database server from Azure portal, navigate to the server overview blade, click on Manage Backups on the navigation menu, and then click on Configure retention at the navigation bar.

![Изменение PITR на портале Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instancetabmanaged-instance"></a>[Управляемый экземпляр](#tab/managed-instance)

Change of PITR backup retention for SQL Database managed instance is performed at an individual database level. To change PITR backup retention for an instance database from Azure portal, navigate to the individual database overview blade, and then click on Configure backup retention at the navigation bar.

![Изменение PITR на портале Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Изменение периода хранения PITR с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Изменение периода хранения PITR с помощью REST API

#### <a name="sample-request"></a>Пример запроса

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Текст запроса

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Пример ответа

Код состояния: 200.

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Дополнительные сведения о политиках краткосрочного хранения резервных копий см. [здесь](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Дальнейшие действия

- Резервные копии базы данных являются важной частью любой стратегии непрерывности бизнес-процессов и аварийного восстановления, так как они защищают данные от случайного повреждения или удаления. Дополнительные сведения о других решениях для Базы данных SQL Azure, обеспечивающих непрерывность бизнес-процессов, см. в статье [Обзор обеспечения непрерывности бизнес-процессов с помощью Базы данных SQL Azure](sql-database-business-continuity.md).
- Сведения о восстановлении базы данных на определенный момент времени с помощью портала Azure см .в [этой статье](sql-database-recovery-using-backups.md).
- Сведения о восстановлении базы данных на определенный момент времени с помощью PowerShell см .в [этой статье](scripts/sql-database-restore-database-powershell.md).
- Сведения о настройке и управлении долгосрочного хранения создаваемых автоматически резервных копий в хранилище BLOB-объектов Azure, а также о восстановлении таких резервных копий на портале Azure см. в статье [Управление долгосрочным хранением резервных копий базы данных SQL Azure](sql-database-long-term-backup-retention-configure.md).
- To configure, manage, and restore from long-term retention of automated backups in Azure Blob storage using PowerShell, see [Manage long-term backup retention using PowerShell](sql-database-long-term-backup-retention-configure.md).
