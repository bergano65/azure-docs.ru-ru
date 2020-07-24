---
title: Журналы — масштабирование (Цитус) — база данных Azure для PostgreSQL
description: Как получить доступ к журналам базы данных Azure для PostgreSQL-Scale (Цитус)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099047"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Журналы в базе данных Azure для PostgreSQL — масштабирование (Цитус)

Журналы PostgreSQL доступны на каждом узле группы серверов "горизонтальное масштабирование (Цитус)". Журналы можно поставлять на сервер хранилища или в службу аналитики. Журналы могут использоваться для обнаружения, устранения и исправления ошибок конфигурации и неоптимальной производительности.

## <a name="accessing-logs"></a>Доступ к журналам

Чтобы получить доступ к журналам PostgreSQL для координатора Цитус или рабочего узла, откройте узел в портал Azure:

![список узлов](media/howto-hyperscale-logging/choose-node.png)

Для выбранного узла откройте **параметры диагностики**и щелкните **+ Добавить параметр диагностики**.

![Кнопка добавления параметров диагностики](media/howto-hyperscale-logging/diagnostic-settings.png)

Выберите имя для новых параметров диагностики и установите флажок **постгрескллогс** .  Выберите назначения, которые должны получить журналы.

![Выбор журналов PostgreSQL](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Приступая к работе с запросами log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- Сведения о [концентраторах событий Azure](/azure/event-hubs/event-hubs-about)
