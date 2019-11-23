---
title: Monitor and Tune in Azure Database for PostgreSQL - Single Server
description: This article describes monitoring and tuning features in Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 799b2b6ee6074472a951e69dec7e9a87056373b4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384028"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>мониторингу и настройке отдельного сервера Базы данных Azure для PostgreSQL
Мониторинг данных о серверах помогает устранять неполадки и оптимизировать рабочую нагрузку. База данных Azure для PostgreSQL предоставляет различные параметры мониторинга, дающие возможность получить полезные сведения о поведении сервера.

## <a name="metrics"></a>Метрики
База данных Azure для PostgreSQL предоставляет различные метрики, позволяющие понять действия ресурсов, поддерживающих сервер PostgreSQL. Каждая метрика создается с частотой раз в минуту. В журнале содержится история о метриках за период в 30 дней. Вы можете настроить оповещения на основе метрик. Пошаговые инструкции см. в статье [Использование портала Azure для настройки оповещений на основе метрик для базы данных Azure для MySQL](howto-alert-on-metric.md). Другие задачи включают настройку автоматических действий, выполнение расширенной аналитики и архивирование журнала. Дополнительные сведения см. в статье [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Список метрик
Для базы данных Azure для PostgreSQL доступны следующие метрики:

|Метрика|Отображаемое имя метрики|Единица|Описание|
|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Процент используемого ЦП.|
|memory_percent|Процент памяти|Процент|Процент используемой памяти.|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Процент используемых операций ввода-вывода.|
|storage_percent|Storage percentage|Процент|Процент использованного объема хранилища сервера (от максимального объема).|
|storage_used|Используемое хранилище|Байты|Используемый объем хранилища. Хранилище, используемое службой, может содержать файлы базы данных, журналы транзакций и журналы сервера.|
|storage_limit|Storage limit|Байты|Максимальный объем хранилища для этого сервера.|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Процент использованного объема хранилища журнала сервера (от максимального объема).|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Используемый объем хранилища журнала сервера.|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Максимальный объем хранилища журнала сервера для этого сервера.|
|active_connections|Активные подключения|Количество|Число активных подключений к серверу.|
|connections_failed|Неудачные подключения|Количество|Число неудачных попыток подключения к серверу.|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Исходящий сетевой трафик по активным подключениям.|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Входящий сетевой трафик по активным подключениям.|
|backup_storage_used|Используемое хранилище резервных копий|Байты|Объем используемого хранилища резервных копий.|
|pg_replica_log_delay_in_bytes|Максимальная задержка между репликами|Байты|The lag in bytes between the master and the most-lagging replica. Эта метрика доступна только на главном сервере.|
|pg_replica_log_delay_in_seconds|Задержка реплики|Секунды|The time since the last replayed transaction. This metric is available for replica servers only.|

## <a name="server-logs"></a>Журналы сервера
Можно включить ведение журнала на сервере. These logs are also available through Azure Diagnostic Logs in [Azure Monitor logs](../azure-monitor/log-query/log-query-overview.md), Event Hubs, and Storage Account. Дополнительные сведения о ведении журналов см. на странице [Журналы сервера в базе данных Azure для PostgreSQL](concepts-server-logs.md).

## <a name="query-store"></a>Хранилище запросов
[Query Store](concepts-query-store.md) keeps track of query performance over time including query runtime statistics and wait events. Эта функция сохраняет сведения о производительности среды выполнения запросов в системной базе данных с именем **azure_sys** в схеме query_store. Вы можете управлять процессами сбора и хранения данных с помощью различных приспособлений конфигурации.

## <a name="query-performance-insight"></a>Сведения о производительности запросов
Средство [Анализ производительности запросов](concepts-query-performance-insight.md) работает совместно с хранилищем запросов для предоставления визуализаций, доступных на портале Azure. Эти диаграммы позволяют определить основные запросы, влияющие на производительность. Query Performance Insightis accessible from the **Support + troubleshooting** section of your Azure Database for PostgreSQL server's portal page.

## <a name="performance-recommendations"></a>Рекомендации по производительности
Функция [Рекомендации по производительности](concepts-performance-recommendations.md) определяет возможности для повышения производительности рабочей нагрузки. Performance Recommendations provides you with recommendations for creating new indexes that have the potential to improve the performance of your workloads. Для создания рекомендаций по индексам эта функция учитывает различные характеристики базы данных, включая ее схемы и рабочую нагрузку по данным хранилища запросов. После реализации любой из рекомендаций по повышению производительности клиенты должны протестировать производительность, чтобы оценить результаты внесенных изменений. 

## <a name="service-health"></a>Работоспособность службы
[Azure Service health](../service-health/overview.md) provides a view of all service health notifications in your subscription. You can set up Service Health alerts to notify you via your preferred communication channels when there are issues or changes that may affect the Azure services and regions you use.

You can view scheduled maintenance events for Azure Database for PostgreSQL - Single Server by using the **planned maintenance** event type. To learn how to create **service health alerts**, visit the [Create activity log alerts on service notifications](../service-health/alerts-activity-log-service-notifications.md) article.

> [!IMPORTANT]
> The planned maintenance notifications is available in preview for EAST US and UK South only.

## <a name="next-steps"></a>Дальнейшие действия
- Рекомендации по созданию оповещений для метрики см. в статье [Настройка оповещений на основе метрик для базы данных Azure для PostgreSQL на портале Azure](howto-alert-on-metric.md).
- Дополнительные сведения о доступе к метрикам и их экспорте с помощью портала Azure, REST API или интерфейса командной строки см. в статье [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Читайте наш блог, чтобы ознакомиться с [рекомендациями по мониторингу сервера](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
