---
title: Матрица служб и средств для переноса данных — Azure | Документация Майкрософт
description: Дополнительные сведения о службах и средствах, доступных для переноса баз данных и для поддержки различных этапов процесса миграции.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 3b3bbe45c4850d1bb37a4d991e323d5f6d9a8a0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532424"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Доступные службы и средства для сценариев переноса данных

Эта статья содержит матрицу Майкрософт, а также сторонние службы и средства, доступные для помощи в различных сценариях перемещения баз данных и данных, а также специальных задачах.

В следующих таблицах приведены службы и средства, которые можно использовать для успешного планирования переноса данных и завершения его различных этапов.

> [!NOTE]
> В следующих таблицах сторонние средства отмечены звездочкой (*).

## <a name="business-justification-phase"></a>Этап коммерческого обоснования

| **Источник** | **Цель** | **Обнаружение /**<br/>**инвентаризация** | **Целевое средство и рекомендация**<br/>**по номеру SKU** | **Совокупная стоимость владения/рентабельность инвестиций и**<br/>**бизнес-модель** |
| --- | --- | --- | --- | --- |
| SQL Server; | База данных Azure SQL | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Миграция Azure](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server; | Управляемый экземпляр Базы данных SQL Azure | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Миграция Azure](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server; | Виртуальная машина с SQL Azure | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Миграция Azure](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server; | Хранилище данных SQL |  |  | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Миграция Azure](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor*](https://www.migvisor.com/) |  |
| Oracle | Хранилище данных SQL | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Миграция Azure](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | База данных Azure для PostgreSQL |  |  |  |
| MongoDB | База данных Cosmos | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | База данных Cosmos |  |  |  |
| MySQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [Миграция Azure](https://azure.microsoft.com/services/azure-migrate/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | База данных Azure для MySQL | [Миграция Azure](https://azure.microsoft.com/services/azure-migrate/) |  | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | База данных Azure для MySQL |  |  | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | База данных Azure для PostgreSQL | [Миграция Azure](https://azure.microsoft.com/services/azure-migrate/) |  | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | База данных Azure для PostgreSQL |  |  | [Калькулятор совокупной стоимости владения](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |  |  |
| Access | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |  |  |
| Sybase | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Этап подготовки к миграции

| **Источник** | **Цель** | **Доступ к данным приложений**<br/>**Оценка слоя** | **База данных**<br/>**Оценка** | **Производительность**<br/>**Оценка** |
| --- | --- | --- | --- | --- |
| SQL Server; | База данных Azure SQL |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server; | Управляемый экземпляр Базы данных SQL Azure |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server; | Виртуальная машина с SQL Azure |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server; | Хранилище данных SQL |  |  |  |
| RDS SQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | Хранилище данных SQL |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | База данных Azure для PostgreSQL |  |  |  |
| MongoDB | База данных Cosmos |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | База данных Cosmos |  |  |  |
| MySQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | База данных Azure для MySQL |  |  |  |
| RDS MySQL | База данных Azure для MySQL |  |  |  |
| PostgreSQL | База данных Azure для PostgreSQL |  |  |  |
| RDS PostgreSQL | База данных Azure для PostgreSQL |  |  |  |
| DB2 | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-phase"></a>Этап миграции

| **Источник** | **Цель** | **Схема** | **Данные**<br/>**(не в сети)** | **Данные**<br/>**(в сети)** |
| --- | --- | --- | --- | --- |
| SQL Server; | База данных Azure SQL | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server; | Управляемый экземпляр Базы данных SQL Azure | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server; | Виртуальная машина с SQL Azure | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server; | Хранилище данных SQL |  |  |  |
| RDS SQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Хранилище данных SQL | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | База данных Azure для PostgreSQL |  |  |  |
| MongoDB | База данных Cosmos | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | База данных Cosmos | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | База данных Azure для MySQL | [mysqldump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | База данных Azure для MySQL | [mysqldump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | База данных Azure для PostgreSQL | [pg_dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | База данных Azure для PostgreSQL | [pg_dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | База данных SQL Azure, Управляемый экземпляр, виртуальная машина | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-phase"></a>Этап после миграции

| **Источник** | **Цель** | **Optimize** (Оптимизация) |
| --- | --- | --- |
| SQL Server; | База данных Azure SQL | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server; | Управляемый экземпляр Базы данных SQL Azure | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server; | Виртуальная машина с SQL Azure | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server; | Хранилище данных SQL |  |
| RDS SQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |
| Oracle | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |
| Oracle | Хранилище данных SQL |  |
| Oracle | База данных Azure для PostgreSQL |  |
| MongoDB | База данных Cosmos | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | База данных Cosmos |  |
| MySQL | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |
| MySQL | База данных Azure для MySQL |  |
| RDS MySQL | База данных Azure для MySQL |  |
| PostgreSQL | База данных Azure для PostgreSQL |  |
| RDS PostgreSQL | База данных Azure для PostgreSQL |  |
| DB2 | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |
| Access | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |
| Sybase | База данных SQL Azure, Управляемый экземпляр, виртуальная машина |  |
| | | |

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения о службе Azure Database Migration Service см. в статье [Что такое Azure Database Migration Service](dms-overview.md).
