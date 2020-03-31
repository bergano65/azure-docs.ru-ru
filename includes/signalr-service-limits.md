---
title: Служба Azure SignalR ограничивает таблицу
description: Описывает пределы системы для службы Azure SignalR.
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: zhshang
ms.openlocfilehash: 1d87439b7afeb946fd37c468a0853adcb674ee5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67185688"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение | 
| --- | --- | --- |
| Подразделения службы Azure SignalR в каждом экземпляре для свободного уровня |1 |1 |
| Единицы службы Azure SignalR в экземпляре стандартного уровня |100 |100 |
| Подразделения службы Azure SignalR за подписку на регион бесплатного уровня|5 |5 |
| Общий объем службы Azure SignalR учитывается на одну подписку в регионе |150 |Неограниченно |
| Подключения на единицу в день, уровень "Бесплатный" |20 |20 |
| Подключения на единицу в день, уровень "Стандартный" |1000 |1000|
| Включенные сообщения на единицу в день, уровень "Бесплатный"|20 000 |20 000 |
| Включенные сообщения на единицу в день, уровень "Стандартный"|1 000 000 |1 000 000 |

Запросите обновление стандартных ограничений для подписки. Для этого отправьте запрос в службу поддержки. 
