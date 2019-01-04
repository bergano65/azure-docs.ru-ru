---
title: База данных SQL Azure | Документация Майкрософт
description: Сведения о том, как настроить Базу данных SQL Azure и управлять ею.
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
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439541"
---
# <a name="how-to-use-azure-sql-database"></a>Использование Базы данных SQL Azure

В этой статье можно найти различные руководства, сценарии и пояснения, которые помогут в администрировании и настройке отдельной Базы данных SQL Azure. Здесь также можно найти определенные руководства для [отдельной базы данных](sql-database-howto-single-database.md) и [Управляемого экземпляра](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Загрузка данных

- [Копирование отдельной базы данных в Azure](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Импорт базы данных из BACPAC-файла](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Экспорт базы данных в BACPAC-файл](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Загрузка данных с помощью BCP](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Загрузка данных с помощью ADF](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Синхронизация данных

- [Синхронизация данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Агент синхронизации данных](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Репликация изменений схемы](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Отслеживание с помощью OMS](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Рекомендации по синхронизации данных](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Устранение ошибок синхронизации данных](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Мониторинг и настройка

-  [Настройка вручную](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Использование динамических административных представлений для мониторинга производительности](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Использование хранилища запросов для мониторинга производительности](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Устранение проблем производительности с помощью Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Использование журнала диагностики Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Мониторинг используемого пространства в хранилище выполняющейся в памяти OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Расширенные события

- [Расширенные события](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Хранение расширенных событий в файлах событий](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Хранение расширенных событий в кольцевом буфере](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Настройка компонентов

- [Настройка аутентификации Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Настройка условного доступа](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Многофакторная проверка подлинности с помощью AAD](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Настройка многофакторной проверки подлинности](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Настройка политики временного хранения](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [Настройка TDE и BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [Смена ключей TDE BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Удаление предохранителя TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Настройка In-Memory OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Настройка службы автоматизации Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Разработка приложений

- [Соединение](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Использование соединителя Spark](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Аутентификация приложения](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Сообщения об ошибках](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [Использование пакетной обработки для лучшей производительности](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Руководство по настройке подключений](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [Псевдонимы DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [Настройка DNS-псевдонимов в PowerShell](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Порты: ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C и C++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Разработка приложений

- [Разработка для аварийного восстановления](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Разработка для эластичных пулов](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Разработка для обновлений приложений](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Разработка мультитенантных приложений SaaS

- [Конструктивные шаблоны SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [Индексатор видео SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [Безопасность приложений SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [практических руководствах по Управляемому экземпляру](sql-database-howto-managed-instance.md).
- Дополнительные сведения о [практических руководствах по отдельной базе данных](sql-database-howto-single-database.md).
