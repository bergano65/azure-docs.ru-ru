---
title: Журналы — масштабирование (Цитус) — база данных Azure для PostgreSQL
description: Как получить доступ к журналам базы данных Azure для PostgreSQL-Scale (Цитус)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: fac0db7f8c94113ff87d1c0a3091e4c0cbd5ed2b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489852"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Журналы в базе данных Azure для PostgreSQL — масштабирование (Цитус)

Журналы PostgreSQL доступны на каждом узле группы серверов "горизонтальное масштабирование (Цитус)". Журналы можно поставлять на сервер хранилища или в службу аналитики. Журналы могут использоваться для обнаружения, устранения и исправления ошибок конфигурации и неоптимальной производительности.

## <a name="accessing-logs"></a>Доступ к журналам

Чтобы получить доступ к журналам PostgreSQL для координатора Цитус или рабочего узла, откройте узел в портал Azure:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="список узлов":::

Для выбранного узла откройте **параметры диагностики**и щелкните **+ Добавить параметр диагностики**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="список узлов":::

Выберите имя для новых параметров диагностики и установите флажок **постгрескллогс** .  Выберите назначения, которые должны получить журналы.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="список узлов":::

## <a name="next-steps"></a>Дальнейшие действия

- [Приступая к работе с запросами log Analytics](../azure-monitor/log-query/get-started-portal.md)
- Сведения о [концентраторах событий Azure](../event-hubs/event-hubs-about.md)