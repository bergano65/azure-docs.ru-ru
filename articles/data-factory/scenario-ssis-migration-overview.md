---
title: Перенос локальных рабочих нагрузок служб SSIS в службы SSIS в фабрике данных Azure
description: Перенос локальных рабочих нагрузок служб SSIS в службы SSIS в ADF.
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
ms.date: 9/3/2019
ms.openlocfilehash: 99b7de8c0e24fdbd12742b26394e61c956638525
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038278"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Перенос локальных рабочих нагрузок служб SSIS в службы SSIS в ADF

## <a name="overview"></a>Обзор

При переносе рабочих нагрузок базы данных из локальной SQL Server в службы баз данных Azure — это база данных SQL Azure или управляемый экземпляр базы данных SQL Azure, рабочие нагрузки ETL на SQL Server Integration Services (SSIS) в качестве одного из основных добавленных значений. Кроме того, необходимо выполнить миграцию служб.

Azure-SSIS Integration Runtime (IR) в фабрике данных Azure (ADF) поддерживает выполнение пакетов служб SSIS. После подготовки Azure-SSIS IR можно использовать привычные средства, такие как SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS), и служебные программы командной строки, такие как дтинсталл/dtutil/dtexec, для развертывания и запуска пакетов в Azure. Дополнительные сведения см. в разделе [Общие сведения о точности и сдвиге Azure SSIS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

В этой статье описывается процесс переноса рабочих нагрузок ETL из локальных служб SSIS в службы SSIS в ADF. Процесс миграции состоит из двух этапов: **оценки** и **миграции**.

## <a name="assessment"></a>Оценка

Для создания полного плана миграции можно определить проблемы с исходными пакетами служб SSIS, которые препятствуют успешной миграции.

Помощник по миграции данных (DMA) — это свободно скачиваемое средство для этой цели, которое можно установить и выполнить локально. Проект оценки DMA типа **Integration Services** можно создать для оценки пакетов служб SSIS в пакетах и определения проблем совместимости, представленных в следующих категориях.

- Блокирование миграции. это проблемы совместимости, которые блокируют запуск пакетов источника миграции на Azure-SSIS IR. DMA предоставляет рекомендации по решению этих проблем.

- Информативные проблемы. Это частично поддерживаемые или устаревшие функции, используемые в исходных пакетах. DMA предоставляет полный набор рекомендаций, альтернативных подходов, доступных в Azure, и устраняет действия по устранению проблемы.

### <a name="four-storage-types-for-ssis-packages"></a>Четыре типа хранилища для пакетов служб SSIS

- Каталог служб SSIS (SSISDB). Это было представлено в SQL Server 2012 и содержит набор хранимых процедур, представлений и функций, возвращающих табличное значение, используемых для работы с проектами и пакетами служб SSIS.
- Файловая система.
- SQL Server системная база данных (MSDB).
- Хранилище пакетов служб SSIS. Это уровень управления пакетами поверх двух подтипов:
  - MSDB — это системная база данных в SQL Server, используемая для хранения пакетов служб SSIS.
  - Управляемая файловая система, которая является определенной папкой в SQL Server пути установки, используемого для хранения пакетов служб SSIS.

Сейчас DMA поддерживает пакетную оценку пакетов, хранящихся в **файловой системе**, **хранилище пакетов**и **каталоге служб SSIS** , с момента **DMA версии 5.0**.

Получите [DMA](https://docs.microsoft.com/sql/dma/dma-overview)и [выполните оценку пакета с его помощью](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Миграция

В зависимости от [типов хранилища](#four-storage-types-for-ssis-packages) исходных пакетов SSIS и назначения миграции рабочих нагрузок базы данных действия по переносу **пакетов служб SSIS** и **Агент SQL Server заданий** , планирующих выполнение пакетов служб SSIS, могут отличаться. Существует два сценария:

- [**Управляемый экземпляр базы данных SQL Azure** как место назначения рабочей нагрузки базы данных](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**База данных SQL Azure** в качестве назначения рабочей нагрузки базы данных](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Управляемый экземпляр базы данных SQL Azure** как место назначения рабочей нагрузки базы данных

| **Тип хранилища пакета** |Пакетная миграция пакетов служб SSIS|Пакетная миграция заданий служб SSIS|
|-|-|-|
|SSISDB|[Перенос **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Миграция заданий служб SSIS в агент управляемого экземпляра базы данных SQL Azure](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Файловая система|Повторно разверните их в файловые ресурсы или файлы Azure через дтинсталл, dtutil или ручную копию или для доступа к файловым системам через виртуальную сеть или локальную среду IR. Дополнительные сведения см. в разделе [программа dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Преобразуйте их в конвейеры и действия/триггеры ADF с помощью сценариев, портала SSMS или ADF. Дополнительные сведения см. в разделе [функция планирования SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Экспортируйте их в файловые системы, файловые ресурсы или файлы Azure с помощью SSMS или dtutil. Дополнительные сведения см. в разделе [Экспорт пакетов служб SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Преобразуйте их в конвейеры и действия/триггеры ADF с помощью сценариев, портала SSMS или ADF. Дополнительные сведения см. в разделе [функция планирования SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Хранилище пакетов|Экспортируйте их в файловые системы, файловые ресурсы или файлы Azure с помощью SSMS/dtutil или повторно разверните их в файловые ресурсы или файлы Azure с помощью дтинсталл/dtutil/вручную и не задерживайте их в файловых системах, чтобы получить доступ через виртуальную сеть или локальную среду IR. Дополнительные сведения см. в разделе Программа dtutil. Дополнительные сведения см. в разделе [программа dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Преобразуйте их в конвейеры и действия/триггеры ADF с помощью сценариев, портала SSMS или ADF. Дополнительные сведения см. в разделе [функция планирования SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**База данных SQL Azure** в качестве назначения рабочей нагрузки базы данных

| **Тип хранилища пакета** |Пакетная миграция пакетов служб SSIS|Как выполнить пакетную миграцию заданий|
|-|-|-|
|SSISDB|Повторное развертывание в Azure-SSISDB с помощью SSDT/SSMS. Дополнительные сведения см. [в разделе Развертывание пакетов служб SSIS в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Преобразуйте их в конвейеры и действия/триггеры ADF с помощью сценариев, портала SSMS или ADF. Дополнительные сведения см. в разделе [функция планирования SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Файловая система|Повторно разверните их в файловые ресурсы или файлы Azure через дтинсталл, dtutil или ручную копию или для доступа к файловым системам через виртуальную сеть или локальную среду IR. Дополнительные сведения см. в разделе [программа dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Преобразуйте их в конвейеры и действия/триггеры ADF с помощью сценариев, портала SSMS или ADF. Дополнительные сведения см. в разделе [функция планирования SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Экспортируйте их в файловые системы, файловые ресурсы или файлы Azure с помощью SSMS или dtutil. Дополнительные сведения см. в разделе [Экспорт пакетов служб SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Преобразуйте их в конвейеры и действия/триггеры ADF с помощью сценариев, портала SSMS или ADF. Дополнительные сведения см. в разделе [функция планирования SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Хранилище пакетов|Экспортируйте их в файловые системы, файловые ресурсы или файлы Azure с помощью SSMS/dtutil или повторно разверните их в файловые ресурсы или файлы Azure с помощью дтинсталл/dtutil/вручную и не задерживайте их в файловых системах, чтобы получить доступ через виртуальную сеть или локальную среду IR. Дополнительные сведения см. в разделе Программа dtutil. Дополнительные сведения см. в разделе [программа dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Преобразуйте их в конвейеры и действия/триггеры ADF с помощью сценариев, портала SSMS или ADF. Дополнительные сведения см. в разделе [функция планирования SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/introduction)
- [Помощник по миграции баз данных](https://docs.microsoft.com/sql/dma/dma-overview)
- [Перемещение рабочих нагрузок служб SSIS в облако](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Миграция пакетов служб SSIS в управляемый экземпляр базы данных SQL Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Повторное развертывание пакетов в базе данных SQL Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Дополнительная информация

- [Проверка пакетов служб SSIS, развернутых в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Запуск пакетов служб SSIS, развернутых в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Мониторинг Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Планирование выполнения пакетов служб SSIS в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
