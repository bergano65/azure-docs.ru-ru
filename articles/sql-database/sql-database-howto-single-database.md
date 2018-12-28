---
title: Настройка отдельной Базы данных SQL Azure | Документация Майкрософт
description: Сведения о том, как настроить отдельную Базу данных SQL Azure и управлять ею.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: d34853220e423e73c6ca8cf7c76ba616b815b8bd
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439751"
---
# <a name="how-to-use-single-database"></a>Использование отдельной базы данных

В этом статье можно найти различные руководства, сценарии и пояснения, которые помогут в администрировании и настройке отдельной Базы данных SQL Azure.

## <a name="migrate"></a>Миграция

- [Перенос Базы данных SQL Server в Базу данных SQL Azure](sql-database-cloud-migrate.md) — содержит информацию о рекомендуемом процессе миграции и средствах для миграции в Управляемый экземпляр.
- [Новый администратор базы данных в облаке — управление базой данных в службе "База данных SQL Azure"](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Настройка компонентов

- [Настройка репликации транзакций](replication-to-sql-database.md) для репликации данных между базами данных.
- [Настройка обнаружения угроз](sql-database-threat-detection.md), чтобы База данных SQL Azure могла идентифицировать подозрительные действия, такие как внедрение кода SQL или доступ из подозрительных мест.
- [Настройка динамического маскирования данных](sql-database-dynamic-data-masking-get-started-portal.md) для защиты конфиденциальных данных.
- [Настройка хранения резервных копий ](sql-database-long-term-backup-retention-configure.md) для базы данных, чтобы сохранять резервные копии в хранилище BLOB-объектов Azure. Альтернативный подход — [настройка долгосрочного хранения резервных копий с помощью хранилища Службы восстановления Azure](sql-database-long-term-backup-retention-configure-vault.md) (не рекомендуется).
- [Настройка георепликации](sql-database-geo-replication-portal.md), чтобы хранить реплику базы данных в другом регионе.
- [Настройка безопасности для георепликации](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Мониторинг и настройка базы данных

- [Включение автоматической настройки](sql-database-automatic-tuning-enable.md), чтобы База данных SQL Azure имела возможность оптимизировать производительность рабочей нагрузки.
- [Включение уведомлений по электронной почте об автоматической настройке](sql-database-automatic-tuning-email-notifications.md), чтобы получать сведения о рекомендациях по настройке.
- [Применение рекомендаций по производительности](sql-database-advisor-portal.md) и оптимизация своей базы данных.
- [Создание оповещений](sql-database-insights-alerts-portal.md) для получения уведомлений из Базы данных SQL Azure.
- [Устранение неполадок с подключением](sql-database-troubleshoot-common-connection-issues.md) при возникновении проблем с подключением между приложениями и базой данных. Вы также можете воспользоваться службой [Работоспособность ресурсов](sql-database-resource-health.md).
- [Управление файловым пространством](sql-database-file-space-management.md), чтобы отслеживать использование хранилища в базе данных.

## <a name="query-distributed-data"></a>Запрашивание распределенных данных

- [Запрос вертикально секционированных данных](sql-database-elastic-query-getting-started-vertical.md) между несколькими базами данных.
- [Получение отчетов по масштабируемым уровням данных](sql-database-elastic-query-horizontal-partitioning.md).
- [Запрос к таблицам с разными схемами](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Задания обработки эластичных баз данных

- [Создание агента заданий обработки эластичных баз данных и управление ими с помощью PowerShell](elastic-jobs-powershell.md).
- [Создание заданий эластичной базы данных и управление ими с помощью Transact-SQL (T-SQL)](elastic-jobs-tsql.md).
- [Перенос данных из старых заданий обработки эластичных БД](elastic-jobs-migrate.md)

## <a name="database-sharding"></a>Сегментирование базы данных

- [Обновление клиентской библиотеки эластичной базы данных](sql-database-elastic-scale-upgrade-client-library.md)
- [Создание сегментированного приложения](sql-database-elastic-scale-get-started.md)
- [Запрос горизонтально секционированных данных](sql-database-elastic-query-getting-started.md).
- [Выполнение запросов к нескольким сегментам](sql-database-elastic-scale-multishard-querying.md).
- [Перемещение сегментированных данных](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Настройка параметров безопасности для сегментов базы данных](sql-database-elastic-scale-split-merge-security-configuration.md).
- [Добавление сегмента в текущий набор сегментов базы данных](sql-database-elastic-scale-add-a-shard.md).
- [Устранение проблем с картой сегментов](sql-database-elastic-database-recovery-manager.md).
- [Перенос сегментированной базы данных](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Создание счетчиков](sql-database-elastic-database-perf-counters.md).
- [Использование Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) для запроса сегментированных данных.
- [Использование платформы Dapper](sql-database-elastic-scale-working-with-dapper.md) для запроса сегментированных данных.

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [практических руководствах в Управляемом экземпляре](sql-database-howto-managed-instance.md).
