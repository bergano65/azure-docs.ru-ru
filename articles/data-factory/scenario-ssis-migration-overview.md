---
title: Перенос рабочих нагрузок SSIS в SSIS на фабрику данных Azure
description: Мигрируйте на предварительных рабочих нагрузках SSIS в SSIS в ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: a588a0977a4c6dcefaaefcfdcc542fee8b15466b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419074"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Миграция локальных рабочих нагрузок служб SSIS в службы SSIS в ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Обзор

При переносе рабочих нагрузок базы данных из сервера S'L в помещения в службы баз данных Azure, а именно на управляемый экземпляр базы данных Azure S'L или управляемые базы данных Azure S'L, необходимо будет также перенести рабочие нагрузки ETL в службы интеграции серверов (SSIS) в качестве одной из основных служб с добавленной стоимостью.

Время интеграции Azure-SSIS (IR) на фабрике данных Azure (ADF) поддерживает запуск пакетов SSIS. После подготовки Azure-SSIS IR можно использовать знакомые инструменты, такие как S'L Server Data Tools (SSDT)/S'L Server Management Studio (SSMS) и утилиты командной строки, такие как dtinstall/dtutil/dtexec, для развертывания и запуска пакетов в Azure. Для получения дополнительной информации смотрите [обзор подъема и смены Azure SSIS.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

В этой статье освещаются процесс миграции рабочих нагрузок ETL от собственного SSIS до SSIS в ADF. Процесс миграции состоит из двух этапов: **оценка** и **миграция**.

## <a name="assessment"></a>Оценка

Для создания полного плана миграции тщательная оценка поможет выявить проблемы с исходными пакетами SSIS, которые предотвратят успешную миграцию.

Помощник по миграции данных (DMA) представляет собой бесплатное скачиваемое средство для этой цели, которое можно установить и запустить локально. Проект оценки DMA **типовых интеграционных услуг** может быть создан для оценки пакетов SSIS в пакетах и выявления проблем совместимости, которые представлены в следующих категориях:

- Блокировщики миграции: Это проблемы совместимости, которые блокируют пакеты источников миграции для выполнения на ИК Azure-SSIS. DMA предоставляет рекомендации, которые помогут вам решить эти проблемы.

- Информационные проблемы: Это частично поддерживаемые или обесточенные функции, которые используются в исходных пакетах. DMA предоставляет полный набор рекомендаций, альтернативные подходы, доступные в Azure, и смягчающие шаги для решения.

### <a name="four-storage-types-for-ssis-packages"></a>Четыре типа хранения для пакетов SSIS

- Каталог SSIS (SSISDB). Это было введено с помощью S'L Server 2012 и содержит набор сохраненных процедур, представлений и функций, ценных на стол, используемых для работы с проектами/пакетами SSIS.
- Файловая система.
- База данных системы сервера S'L (MSDB).
- Магазин пакетов SSIS. Это слой управления пакетами поверх двух подтипов:
  - MSDB, которая представляет собой системную базу данных в сервере S'L, используемую для хранения пакетов SSIS.
  - Управляемая файловая система, которая представляет собой конкретную папку в пути установки сервера S'L, используемую для хранения пакетов SSIS.

DMA в настоящее время поддерживает пакет-оценку пакетов, хранящихся в **файловой системе,** **пакетном магазине**и **каталоге SSIS** с **dMA-версии v5.0**.

Получить [DMA](https://docs.microsoft.com/sql/dma/dma-overview), и [выполнить ваш пакет оценки с ним](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Миграция

В зависимости от [типов хранилищ](#four-storage-types-for-ssis-packages) исходных пакетов SSIS и назначения миграционных заданий рабочих нагрузок баз данных, этапы переноса **пакетов SSIS** и **заданий серверного агента S'L,** которые планируют выполнение пакетов SSIS, могут отличаться. Имеется два сценария.

- [**База данных Azure S'L управляет экземпляром** как пункт назначения рабочей нагрузки базы данных](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**База данных Azure S'L** как пункт назначения рабочей нагрузки базы данных](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**База данных Azure S'L управляет экземпляром** как пункт назначения рабочей нагрузки базы данных

| **Тип хранения упаковки** |Как пакетно-мигрировать пакеты SSIS|Как к пакет-миграции SSIS рабочих мест|
|-|-|-|
|Ssisdb|[Мигрировать **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Перенос заданий SSIS в управляемый экземпляр базы данных Azure S'L](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Файловая система|Перераспределите их для файла акций/файлов Azure через dtinstall/dtutil/manual copy или для сохранения в файловых системах доступа через VNet/Self-Hosted IR. Для получения дополнительной информации, [см.](https://docs.microsoft.com/sql/integration-services/dtutil-utility)|<li> Мигрируйте с помощью «Волшебника миграции рабочих мест SSIS в SSMS». (how-to-migrate-ssis-job-ssms.md) <li>Преобразуйте их в конвейеры ADF/деятельности/триггеры с помощью скриптов/портала SSMS/ADF. Для получения дополнительной [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)информации см.|
|Сервер S'L (MSDB)|Экспортировать их в файлы систем/ файловых акций/ Лазурные файлы через SSMS/dtutil. Для получения дополнительной [информации см.](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)|Преобразуйте их в конвейеры ADF/деятельности/триггеры с помощью скриптов/портала SSMS/ADF. Для получения дополнительной [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)информации см.|
|Хранилище пакетов|Экспортируйте их в файлы систем/файловых файлов/Лазурные файлы через SSMS/dtutil или передислоцируйте их для файла акций/файлов оговоренных файлов через dtinstall/dtutil/manual copy или храните их в файловых системах для доступа через VNet/Self-Hosted IR. Для получения дополнительной информации, см. Для получения дополнительной информации, [см.](https://docs.microsoft.com/sql/integration-services/dtutil-utility)|Преобразуйте их в конвейеры ADF/деятельности/триггеры с помощью скриптов/портала SSMS/ADF. Для получения дополнительной [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)информации см.|

### <a name="azure-sql-database-as-database-workload-destination"></a>**База данных Azure S'L** как пункт назначения рабочей нагрузки базы данных

| **Тип хранения упаковки** |Как пакетно-мигрировать пакеты SSIS|Как к пакет-мигрировать задания|
|-|-|-|
|Ssisdb|Передислокация в Azure-SSISDB через SSDT/SSMS. Для получения дополнительной информации смотрите [Развертывание пакетов SSIS в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Преобразуйте их в конвейеры ADF/деятельности/триггеры с помощью скриптов/портала SSMS/ADF. Для получения дополнительной [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)информации см.|
|Файловая система|Перераспределите их для файла акций/файлов Azure через dtinstall/dtutil/manual copy или для сохранения в файловых системах доступа через VNet/Self-Hosted IR. Для получения дополнительной информации, [см.](https://docs.microsoft.com/sql/integration-services/dtutil-utility)|<li> Мигрируйте с помощью «Волшебника миграции рабочих мест SSIS в SSMS». (how-to-migrate-ssis-job-ssms.md) <li> Преобразуйте их в конвейеры ADF/деятельности/триггеры с помощью скриптов/портала SSMS/ADF. Для получения дополнительной [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)информации см.|
|Сервер S'L (MSDB)|Экспортировать их в файлы систем/ файловых акций/ Лазурные файлы через SSMS/dtutil. Для получения дополнительной [информации см.](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)|Преобразуйте их в конвейеры ADF/деятельности/триггеры с помощью скриптов/портала SSMS/ADF. Для получения дополнительной [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)информации см.|
|Хранилище пакетов|Экспортируйте их в файлы систем/файловых файлов/Лазурные файлы через SSMS/dtutil или передислоцируйте их для файла акций/файлов оговоренных файлов через dtinstall/dtutil/manual copy или храните их в файловых системах для доступа через VNet/Self-Hosted IR. Для получения дополнительной информации, см. Для получения дополнительной информации, [см.](https://docs.microsoft.com/sql/integration-services/dtutil-utility)|Преобразуйте их в конвейеры ADF/деятельности/триггеры с помощью скриптов/портала SSMS/ADF. Для получения дополнительной [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)информации см.|

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/introduction).
- [Помощник по миграции баз данных](https://docs.microsoft.com/sql/dma/dma-overview)
- [Поднимите и переместите рабочие нагрузки SSIS в облако](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Перенос пакетов Integration Services в управляемый экземпляр Базы данных SQL Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Переразвертывание пакетов в базу данных Azure S'L](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Дальнейшие действия

- [Проверка пакетов SSIS, развертываемых в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Запуск пакетов SSIS, развернутых в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Мониторинг времени запуска интеграции Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Расписание выполнения пакета SSIS в Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
