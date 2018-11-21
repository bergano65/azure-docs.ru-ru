---
title: Оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL"
description: В этой статье описывается оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL".
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8b8d1a5a16a948953838f51edca091121dc14df1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628834"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL" 
В этой статье описывается оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL".

## <a name="using-pgstatsstatements"></a>Использование pg_stats_statements
**Pg_stat_statements** является расширением PostgreSQL, которое по умолчанию включено в Базе данных Azure для PostgreSQL. Оно предоставляет средства для отслеживания статистики выполнения всех инструкций SQL, выполняемых сервером. Однако этот модуль подключается к каждому выполнению запроса и ухудшает производительность. Если включить **pg_stat_statements**, текст запроса записывается в файлы на диск.

Клиентам, которые имеют уникальные запросы с длинным текстом или не отслеживают **pg_stat_statements** активно, для максимальной производительности рекомендуем отключить **pg_stat_statements**, установив `pg_stat_statements.track = NONE`.

В некоторых рабочих нагрузках клиентов производительность увеличивалась на 50 % при отключении **pg_stat_statements**. Тем не менее если отключить pg_stat_statements, вы не сможете устранять проблемы с производительностью.

Чтобы задать `pg_stat_statements.track = NONE`:

- На портале Azure перейдите на [страницу управления ресурсами PostgreSQL и выберите колонку параметров сервера](howto-configure-server-parameters-using-portal.md).

![Колонка параметров сервера PostgreSQL](.\media\howto-optimize-query-stats-collection\pg_stats_statements_portal.png)

- Используя [Azure CLI](howto-configure-server-parameters-using-cli.md), выполните команду az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Использование хранилища запросов 
Компонент [хранилища запросов](concepts-query-store.md) в службе "База данных Azure для PostgreSQL" предоставляет эффективный метод для отслеживания статистики запросов и рекомендуется в качестве альтернативы *pg_stats_statements*. 

## <a name="next-steps"></a>Дополнительная информация
Попробуйте задать `pg_stat_statements.track = NONE` на [портале Azure](howto-configure-server-parameters-using-portal.md) или с помощью [Azure CLI](howto-configure-server-parameters-using-cli.md).

Дополнительные сведения см. в статье [Сценарии использования хранилища запросов](concepts-query-store-scenarios.md) и [Рекомендации по использованию хранилища запросов](concepts-query-store-best-practices.md). 
