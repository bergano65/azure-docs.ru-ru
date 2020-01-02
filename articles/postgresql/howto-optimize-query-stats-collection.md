---
title: Оптимизация сбора статистики запросов — База данных Azure для PostgreSQL — один сервер
description: В этой статье описывается, как оптимизировать сбор статистики запросов в базе данных Azure для PostgreSQL-Single Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770175"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Оптимизация сбора статистики запросов в базе данных Azure для PostgreSQL — один сервер
В этой статье описывается оптимизация сбора статистики запросов на сервере службы "База данных Azure для PostgreSQL".

## <a name="use-pg_stats_statements"></a>Использование pg_stats_statements
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

Дополнительные сведения см. здесь: 
- [Сценарии использования хранилища запросов](concepts-query-store-scenarios.md) 
- [Рекомендации по хранилищу запросов](concepts-query-store-best-practices.md) 
