---
title: Мониторинг — база данных Azure для MariaDB
description: В этой статье описываются метрики для мониторинга и создания предупреждений в Базе данных Azure для MariaDB, включая ЦП, хранилище и статистику подключений.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 15a396a86103f41f49d3b49878ec51c1e71add40
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772485"
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

Можно включить ведение журнала медленных запросов на сервере. Эти журналы также доступны в журналах диагностики Azure в Azure Monitor журналов, концентраторах событий и учетной записи хранения. Дополнительные сведения о ведении журналов см. в разделе  [Журналы сервера в базе данных Azure для MySQL](concepts-server-logs.md).

## <a name="query-store"></a>Хранилище запросов

[Хранилище запросов](concepts-query-store.md) отслеживает производительность запросов с течением времени, включая статистику времени выполнения запросов и события ожидания. Эта функция сохраняет сведения о производительности среды выполнения запросов в схеме **MySQL** . Вы можете управлять процессами сбора и хранения данных с помощью различных приспособлений конфигурации.

## <a name="query-performance-insight"></a>Сведения о производительности запросов

Средство [Анализ производительности запросов](concepts-query-performance-insight.md) работает совместно с хранилищем запросов для предоставления визуализаций, доступных на портале Azure. Эти диаграммы позволяют определить основные запросы, влияющие на производительность. Анализ производительности запросов доступен в разделе **интеллектуальные Performance** на странице портала сервера базы данных Azure для MariaDB.

## <a name="performance-recommendations"></a>Рекомендации по производительности

Функция [Рекомендации по производительности](concepts-performance-recommendations.md) определяет возможности для повышения производительности рабочей нагрузки. Рекомендации по производительности предоставляют рекомендации по созданию новых индексов, которые могут повысить производительность рабочих нагрузок. Для создания рекомендаций по индексам эта функция учитывает различные характеристики базы данных, включая ее схемы и рабочую нагрузку по данным хранилища запросов. После реализации любой из рекомендаций по повышению производительности клиенты должны протестировать производительность, чтобы оценить результаты внесенных изменений.

## <a name="service-health"></a>Работоспособность службы
[Служба работоспособности служб Azure](../service-health/overview.md) предоставляет представление всех уведомлений о работоспособности службы в вашей подписке. Вы можете настроить оповещения о работоспособности службы, чтобы уведомлять вас по предпочтительным каналам связи при возникновении проблем или изменений, которые могут повлиять на используемые службы Azure и регионы.

События запланированного обслуживания для базы данных Azure для MariaDB можно просмотреть с помощью типа события **планового обслуживания** . Сведения о создании **оповещений о работоспособности службы**см. в статье [Создание оповещений журнала действий в службе уведомлений](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> Уведомления о плановом обслуживании доступны в предварительной версии только для восточной части США и южная часть Соединенного Королевства.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о доступе к метрикам и их экспорте с помощью портала Azure, REST API или интерфейса командной строки см. в статье [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - См. рекомендации по созданию оповещений для метрики в статье [Как настраивать оповещения](howto-alert-metric.md).
