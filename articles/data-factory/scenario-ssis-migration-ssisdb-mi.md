---
title: Миграция служб SSIS с помощью Управляемый экземпляр Azure SQL в качестве назначения рабочей нагрузки базы данных
description: Миграция служб SSIS с помощью Управляемый экземпляр Azure SQL в качестве назначения рабочей нагрузки базы данных.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 6de08faee78deeb86117084b420eb5043153f62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88186052"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Миграция служб SSIS с помощью Управляемый экземпляр Azure SQL в качестве назначения рабочей нагрузки базы данных

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

При переносе рабочих нагрузок базы данных из SQL Server экземпляра в Управляемый экземпляр SQL Azure необходимо ознакомиться со [службой миграции данных Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) и [сетевыми топологиями для миграции SQL управляемый экземпляр с помощью DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Эта статья посвящена миграции пакетов служб интеграции SQL Server (SSIS), хранящихся в каталоге SSIS (SSISDB), и агент SQL Server заданий, планирующих выполнение пакетов служб SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Перенос каталога служб SSIS (SSISDB)

Миграцию SSISDB можно выполнить с помощью DMS, как описано в статье [Миграция пакетов служб SSIS в SQL управляемый экземпляр](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Задания служб SSIS для агента SQL Управляемый экземпляр

SQL Управляемый экземпляр имеет собственный планировщик первого класса, так же как агент SQL Server локально.  [Пакеты служб SSIS можно запускать с помощью агента Azure SQL управляемый экземпляр](how-to-invoke-ssis-package-managed-instance-agent.md).

Так как средство миграции для заданий служб SSIS пока недоступно, их необходимо перенести из локальной агент SQL Server в агент SQL Управляемый экземпляр с помощью сценариев или вручную.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/introduction).
- [Среда выполнения интеграции Azure SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Миграция баз данных Azure](https://docs.microsoft.com/azure/dms/dms-overview)
- [Сетевые топологии для миграции SQL Управляемый экземпляр с помощью DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Миграция пакетов служб SSIS в Управляемый экземпляр SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Дальнейшие шаги

- [Подключение к каталогу SSIS (SSISDB) в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Запуск пакетов служб SSIS, развернутых в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
