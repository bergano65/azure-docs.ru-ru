---
title: Журналы — масштабирование (Цитус) — база данных Azure для PostgreSQL
description: Как получить доступ к журналам базы данных Azure для PostgreSQL-Scale (Цитус)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: f8840d5115cb552ed203705d37f8c692b3418947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492341"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Журналы в базе данных Azure для PostgreSQL — масштабирование (Цитус)

Журналы PostgreSQL доступны на каждом узле группы серверов "горизонтальное масштабирование (Цитус)". Журналы можно поставлять на сервер хранилища или в службу аналитики. Журналы могут использоваться для обнаружения, устранения и исправления ошибок конфигурации и неоптимальной производительности.

## <a name="accessing-logs"></a>Доступ к журналам

Чтобы получить доступ к журналам PostgreSQL для координатора Цитус или рабочего узла, откройте узел в портал Azure:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="список узлов":::

Для выбранного узла откройте **параметры диагностики** и щелкните **+ Добавить параметр диагностики**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Кнопка добавления параметров диагностики":::

Выберите имя для новых параметров диагностики и установите флажок **постгрескллогс** .  Выберите назначения, которые должны получить журналы.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Выбор журналов PostgreSQL":::

## <a name="next-steps"></a>Дальнейшие действия

- [Приступая к работе с запросами log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)
- Сведения о [концентраторах событий Azure](../event-hubs/event-hubs-about.md)