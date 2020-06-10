---
title: Метрики и оповещения для услуги преобразования сетевых адресов (NAT) виртуальной сети Azure
titleSuffix: Azure Virtual Network
description: Узнайте о метриках и оповещениях Azure Monitor, доступных для NAT виртуальной сети.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 7b4f433ec5782c7a819ffcd6f48bf277d7b13de2
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84341360"
---
# <a name="azure-virtual-network-nat-metrics"></a>Метрики NAT виртуальной сети Azure

Ресурсы шлюза NAT виртуальной сети Azure предоставляют многомерные метрики. Эти метрики можно использовать для отслеживания операций и [устранения неполадок](troubleshoot-nat.md).  Вы можете настроить оповещения для критически важных проблем, таких как нехватка SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT виртуальной сети для исходящего интернет-трафика">
</p>

*Рисунок. NAT виртуальной сети для исходящего интернет-трафика*

## <a name="metrics"></a>Метрики

Ресурсы шлюза NAT предоставляют следующие многомерные метрики в Azure Monitor:

| Метрика | Описание | Рекомендуемая статистическая обработка | Измерения |
|---|---|---|---|
| Байты | Обработано байт входящего и исходящего трафика | SUM | Направление (In; Out), протокол (6 TCP; 17 UDP) |
| Пакеты | Обработано пакетов входящего и исходящего трафика | SUM | Направление (In; Out), протокол (6 TCP; 17 UDP) |
| Отброшенные пакеты | Пакеты, отброшенные шлюзом NAT | SUM | / |
| Количество подключений SNAT | Число переходов состояния за интервал | SUM | Состояние подключения, протокол (6 TCP; 17 UDP) |
| Общее количество подключений SNAT | Текущие активные подключения SNAT (~ используются порты SNAT) | SUM | Протокол (6 TCP; 17 UDP) |


## <a name="alerts"></a>видны узлы

Оповещения для метрик можно настроить в Azure Monitor для каждой из предыдущих [метрик](#metrics).

## <a name="limitations"></a>Ограничения

Работоспособность ресурсов не поддерживается.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [NAT виртуальной сети](nat-overview.md).
* Дополнительные сведения о [ресурсе шлюза NAT](nat-gateway-resource.md).
* Дополнительные сведения об [Azure Monitor](../azure-monitor/overview.md).
* Дополнительные сведения об [устранении неполадок ресурсов шлюза NAT](troubleshoot-nat.md).
* [Расскажите в UserVoice, что нам следует создать далее для NAT виртуальной сети](https://aka.ms/natuservoice).


