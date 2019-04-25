---
title: Оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL"
description: В этой статье описывается оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL".
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/25/2018
ms.date: 02/18/2019
ms.openlocfilehash: 428a22f79008130448bef65f14322d6880b1b367
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422715"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql-server"></a>Оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL" 
В этой статье описывается оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL".

## <a name="use-pgstatsstatements"></a>Использование pg_stats_statements
**Pg_stat_statements** является расширением PostgreSQL, которое по умолчанию включено в Базе данных Azure для PostgreSQL. Оно предоставляет средства для отслеживания статистики выполнения всех инструкций SQL, выполняемых сервером. Этот модуль подключается к каждому выполнению запроса и потребляет большие средства на производительность. Если включить **pg_stat_statements**, текст запроса записывается в файлы на диск.

Если вы создаете уникальные сложные запросы или не отслеживаете **pg_stat_statements** активно, отключите **pg_stat_statements** для лучшей производительности. Чтобы сделать это, измените параметр так: `pg_stat_statements.track = NONE`.

У некоторых клиентов рабочая нагрузка на производительность уменьшилась на 50 % при отключении **pg_stat_statements**. Если отключить pg_stat_statements, вы не сможете устранять проблемы с производительностью.

Чтобы задать `pg_stat_statements.track = NONE`:

- На портале Azure перейдите на [страницу управления ресурсами PostgreSQL и выберите колонку параметров сервера](howto-configure-server-parameters-using-portal.md).

  ![Колонка параметров сервера PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Используйте [Azure CLI](howto-configure-server-parameters-using-cli.md), чтобы выполнить команду az postgres server configuration, для которой задано `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Использование хранилища запросов 
Компонент [Хранилище запросов](concepts-query-store.md) в Базе данных Azure для PostgreSQL предоставляет более эффективный метод отслеживания статистики запросов. Этот компонент рекомендуется в качестве альтернативы использованию *pg_stats_statements*. 

## <a name="next-steps"></a>Дальнейшие действия
Попробуйте задать `pg_stat_statements.track = NONE` на [портале Azure](howto-configure-server-parameters-using-portal.md) или с помощью [Azure CLI](howto-configure-server-parameters-using-cli.md).

Дополнительные сведения можно найти в разделе  
- [Сценарии использования хранилища запросов](concepts-query-store-scenarios.md) 
- [Рекомендации по хранилищу запросов](concepts-query-store-best-practices.md) 
