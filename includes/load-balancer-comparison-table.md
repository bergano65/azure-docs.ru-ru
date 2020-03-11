---
title: включить файл
description: включить файл
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202504"
---
| | Load Balancer (цен. категория "Стандартный") | Azure Load Balancer, категория "Базовый" |
| --- | --- | --- |
| [Размер серверного пула](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Поддерживается до 1000 экземпляров. | Поддерживается до 300 экземпляров. |
| Конечные точки серверного пула | Виртуальные машины или масштабируемые наборы виртуальных машин в одной виртуальной сети. | Виртуальные машины в одной группе доступности или масштабируемом наборе виртуальных машин. |
| [Проверки работоспособности](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Реакция на сбой проверки работоспособности](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-подключения остаются активными при сбое проверки экземпляра __и__ сбое всех проверок. | TCP-подключения остаются активными при сбое проверки экземпляра. Все TCP-подключения завершаются при сбое всех проверок. |
| зоны доступности; | Избыточные между зонами и зональные внешние интерфейсы для входящего и исходящего трафика. | Недоступно |
| Диагностика | [Многомерные метрики Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Журналы Azure Monitor](../articles/load-balancer/load-balancer-monitor-log.md) |
| Порты HA | [Доступно для внутреннего Load Balancer](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Недоступно |
| Обеспечение безопасности по умолчанию | Закрыто для входящих потоков, если иное не разрешено группой безопасности сети. Обратите внимание, что разрешен внутренний трафик из виртуальной сети во внутреннюю подсистему балансировки нагрузки. | Открыты по умолчанию. Группа безопасности сети необязательна. |
| Правила для исходящего трафика | [Декларативная конфигурация NAT для исходящего трафика](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Недоступно |
| Сброс подключений TCP в режиме простоя | [Доступно для любого правила](../articles/load-balancer/load-balancer-tcp-reset.md) | Недоступно |
| [Несколько внешних интерфейсов](../articles/load-balancer/load-balancer-multivip-overview.md) | Входящий и [исходящий](../articles/load-balancer/load-balancer-outbound-connections.md). | Только входящий |
| Операции управления | Большинство операций длятся менее 30 секунд | Обычно 60–90 и более секунд |
| Соглашение об уровне обслуживания | [99,99 %](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Недоступно | 
