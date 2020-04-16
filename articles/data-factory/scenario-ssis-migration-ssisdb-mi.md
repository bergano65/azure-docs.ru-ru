---
title: Миграция SSIS с помощью базы данных Azure S'L управляет экземпляром в качестве пункта назначения рабочей нагрузки базы данных
description: Миграция SSIS с помощью базы данных Azure S'L управляла экземпляром в качестве пункта назначения рабочей нагрузки базы данных.
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
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419057"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Миграция SSIS с помощью базы данных Azure S'L управляет экземпляром в качестве пункта назначения рабочей нагрузки базы данных

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

При переносе рабочих нагрузок баз данных с сервера S'L server в помещения в управляемый экземпляр базы данных Azure S'L, вы должны быть знакомы с [службой миграции данных Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), а [сетевые топологии для базы данных Azure S'L, управляемые экземплярами, с помощью DMS.](https://docs.microsoft.com/azure/dms/resource-network-topologies)

В этой статье основное внимание уделяется миграции пакетов S'L Server Integration Service (SSIS), хранящихся в каталоге SSIS (SSISDB) и заданий серверного агента S'L, которые планируют выполнение пакетов SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Мигрировать каталог SSIS (SSISDB)

Миграция SSISDB может быть осуществлена с помощью DMS, как описано в статье: [Перенос пакетов SSIS в управляемый экземпляр базы данных Azure S'L.](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Задания SSIS для управляемого агента службы данных azure S'L

В управляемом экземпляре базы данных Azure S'L имеется наивный первоклассный планировщик, как и в помещении серверный агент S'L.  Поскольку инструмент миграции для заданий SSIS еще не доступен, они должны быть перенесены из серверного агента S'L в помещение в управляемый экземпляр базы данных Azure s'L с помощью скриптов/ручной копии.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/introduction).
- [Среда выполнения интеграции Azure SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Сетевые топологии для базы данных Azure S'L управляютмиграциями экземпляров с помощью DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Перенос пакетов SSIS в управляемый экземпляр базы данных Azure S'L](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Дальнейшие действия

- [Подключение к каталогу SSIS (SSISDB) в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Запуск пакетов SSIS, развернутых в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
