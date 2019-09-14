---
title: Миграция служб SSIS с управляемым экземпляром базы данных SQL Azure в качестве назначения рабочей нагрузки базы данных | Документация Майкрософт
description: Миграция служб SSIS с управляемым экземпляром базы данных SQL Azure в качестве назначения рабочей нагрузки базы данных.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c1bb056909641ad0b8654e84f35c4fbd5b38db82
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968527"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Миграция служб SSIS с управляемым экземпляром базы данных SQL Azure в качестве назначения рабочей нагрузки базы данных

При переносе рабочих нагрузок базы данных из локальной SQL Server в управляемый экземпляр базы данных SQL Azure вы должны быть знакомы со [службой миграции данных Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) и [сетевыми топологиями для миграций управляемого экземпляра базы данных SQL Azure. Использование DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Эта статья посвящена миграции пакетов служб интеграции SQL Server (SSIS), хранящихся в каталоге SSIS (SSISDB), и агент SQL Server заданий, планирующих выполнение пакетов служб SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Перенос каталога служб SSIS (SSISDB)

Миграцию SSISDB можно выполнить с помощью DMS, как описано в статье: [Перенос пакетов служб SSIS в управляемый экземпляр базы данных SQL Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Задание служб SSIS для агента управляемого экземпляра базы данных SQL Azure

Управляемый экземпляр базы данных SQL Azure имеет собственный планировщик первого класса, точно так же, как агент SQL Server локально.  Так как средство миграции для заданий служб SSIS пока недоступно, они должны быть перенесены агент SQL Server из локальной среды в управляемый агент экземпляра базы данных SQL Azure с помощью сценариев или ручного копирования.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Сетевые топологии для миграции управляемого экземпляра базы данных SQL Azure с помощью DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Миграция пакетов служб SSIS в управляемый экземпляр базы данных SQL Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Следующие шаги

- [Подключение к каталогу SSIS (SSISDB) в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Запуск пакетов служб SSIS, развернутых в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
