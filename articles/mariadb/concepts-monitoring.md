---
title: Мониторинг в Базе данных Azure для MariaDB
description: В этой статье описываются метрики для мониторинга и создания предупреждений в Базе данных Azure для MariaDB, включая ЦП, хранилище и статистику подключений.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3c25798be8af26c1f5e5c1178395cd1688bb132
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382051"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Мониторинг в Базе данных Azure для MariaDB
Мониторинг данных о серверах помогает устранять неполадки и оптимизировать рабочую нагрузку. База данных Azure для MariaDB предоставляет различные метрики, позволяющие понять действия сервера.

## <a name="metrics"></a>Метрики
Все метрики Azure записываются ежеминутно, и каждая из них предоставляет данные за последние 30 дней. Вы можете настроить оповещения на основе метрик. Другие задачи включают настройку автоматических действий, выполнение расширенной аналитики и архивирование журнала. Дополнительные сведения см. в статье [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Пошаговые инструкции см. в статье [Использование портала Azure для настройки оповещений на основе метрик для базы данных Azure для MySQL](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Список метрик
Для Базы данных Azure для MariaDB доступны следующие метрики:

|Метрика|Отображаемое имя метрики|Единица|Описание|
|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Процент используемого ЦП.|
|memory_percent|Процент памяти|Процент|Процент используемой памяти.|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Процент используемых операций ввода-вывода.|
|storage_percent|Storage percentage|Процент|Процент использованного объема хранилища сервера (от максимального объема).|
|storage_used|Используемое хранилище|Байты|Используемый объем хранилища. Хранилище, используемое службой, может содержать файлы базы данных, журналы транзакций и журналы сервера.|
|serverlog_storage_percent|Процент хранилища для журнала сервера|Процент|Процент использованного объема хранилища журнала сервера (от максимального объема).|
|serverlog_storage_usage|Используемый объем хранилища для журнала сервера|Байты|Используемый объем хранилища журнала сервера.|
|serverlog_storage_limit|Максимальный объем хранилища для журнала сервера|Байты|Максимальный объем хранилища журнала сервера для этого сервера.|
|storage_limit|Storage limit|Байты|Максимальный объем хранилища для этого сервера.|
|active_connections|Активные подключения|Количество|Число активных подключений к серверу.|
|connections_failed|Неудачные подключения|Количество|Число неудачных попыток подключения к серверу.|
|network_bytes_egress|Сеть (исходящий трафик)|Байты|Исходящий сетевой трафик по активным подключениям.|
|network_bytes_ingress|Сеть (входящий трафик)|Байты|Входящий сетевой трафик по активным подключениям.|

## <a name="server-logs"></a>Журналы сервера

Можно включить ведение журнала медленных запросов на сервере. These logs are also available through Azure Diagnostic Logs in Azure Monitor logs, Event Hubs, and Storage Account. Дополнительные сведения о ведении журналов см. в разделе  [Журналы сервера в базе данных Azure для MySQL](concepts-server-logs.md).

## <a name="query-store"></a>Хранилище запросов

[Query Store](concepts-query-store.md) keeps track of query performance over time including query runtime statistics and wait events. The feature persists query runtime performance information in the **mysql** schema. Вы можете управлять процессами сбора и хранения данных с помощью различных приспособлений конфигурации.

## <a name="query-performance-insight"></a>Сведения о производительности запросов

Средство [Анализ производительности запросов](concepts-query-performance-insight.md) работает совместно с хранилищем запросов для предоставления визуализаций, доступных на портале Azure. Эти диаграммы позволяют определить основные запросы, влияющие на производительность. Query Performance Insight is accessible in the **Intelligent Performance** section of your Azure Database for MariaDB server's portal page.

## <a name="performance-recommendations"></a>Рекомендации по производительности

Функция [Рекомендации по производительности](concepts-performance-recommendations.md) определяет возможности для повышения производительности рабочей нагрузки. Performance Recommendations provides you with recommendations for creating new indexes that have the potential to improve the performance of your workloads. Для создания рекомендаций по индексам эта функция учитывает различные характеристики базы данных, включая ее схемы и рабочую нагрузку по данным хранилища запросов. После реализации любой из рекомендаций по повышению производительности клиенты должны протестировать производительность, чтобы оценить результаты внесенных изменений.

## <a name="service-health"></a>Работоспособность службы
[Azure Service health](../service-health/overview.md) provides a view of all service health notifications in your subscription. You can set up Service Health alerts to notify you via your preferred communication channels when there are issues or changes that may affect the Azure services and regions you use.

You can view scheduled maintenance events for Azure Database for MariaDB by using the **planned maintenance** event type. To learn how to create **service health alerts**, visit the [Create activity log alerts on service notifications](../service-health/alerts-activity-log-service-notifications.md) article.

> [!IMPORTANT]
> The planned maintenance notifications is available in preview for EAST US and UK South only.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о доступе к метрикам и их экспорте с помощью портала Azure, REST API или интерфейса командной строки см. в статье [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - См. рекомендации по созданию оповещений для метрики в статье [Как настраивать оповещения](howto-alert-metric.md).
