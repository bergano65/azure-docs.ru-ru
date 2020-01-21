---
title: Что такое Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Обзор функций Azure Load Balancer, его архитектуры и реализации. Узнайте, как работает Load Balancer и его использование в облаке.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045399"
---
# <a name="what-is-azure-load-balancer"></a>Что такое Azure Load Balancer?

*Балансировка нагрузки* обозначает процесс равномерного распределения нагрузки (входящего сетевого трафика) в группе серверных ресурсов или серверов. Azure предоставляет [несколько технологий балансировки нагрузки](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview), из которых вы можете выбрать наиболее подходящую для конкретных задач. В этом документе описывается Azure Load Balancer.

Служба Azure Load Balancer работает на уровне 4 модели OSI (Open Systems Interconnection). Она служит единственной точкой взаимодействия с клиентами. Load Balancer распределяет новые входящие потоки, поступающие на внешний интерфейс подсистемы балансировки нагрузки, в экземпляры серверных пулов. Эти потоки соответствуют настроенным правилам балансировки нагрузки и пробам работоспособности. В серверный пул могут входить виртуальные машины Azure или экземпляры масштабируемого набора виртуальных машин.


**[Общедоступный Load Balancer](./concepts-limitations.md#publicloadbalancer)** обеспечивает исходящие подключения для виртуальных машин в виртуальной сети. Эти подключения выполняются путем преобразования их частных IP-адресов в общедоступные. Общедоступные Load Balancer используются для балансировки трафика, направленного из Интернета к виртуальным машинам.

**[Внутренний (или частный) Load Balancer](./concepts-limitations.md#internalloadbalancer)** используется там, где частные IP-адреса необходимы только в интерфейсной части. Внутренние Load Balancer используются для балансировки трафика внутри виртуальной сети. В гибридном сценарии ко внешнему интерфейсу Load Balancer можно подключиться из локальной сети.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Рисунок. Балансировка многоуровневых приложений с помощью общедоступных и внутренних Load Balancer*

Дополнительные сведения об отдельных компонентах Load Balancer см. в разделе [Load Balancer components and limitations](./concepts-limitations.md) (Компоненты и ограничения Azure Load Balancer)

>[!NOTE]
> Корпорация Майкрософт рекомендует использовать [Load Balancer (цен. категория "Стандартный")](./load-balancer-standard-overview.md).
Изолированные виртуальные машины, наборы доступности и масштабируемые наборы виртуальных машин можно подключить только к одному SKU, но никогда к обоим. При использовании общедоступных IP-адресов номера SKU Load Balancer и IP-адреса должны совпадать. SKU Load Balancer и общедоступные IP-адреса неизменяемы.

## <a name="why-use-azure-load-balancer"></a>Зачем нужен Azure Load Balancer?
C помощью Azure Load Balancer можно масштабировать приложения и создавать службы с высоким уровнем доступности. Load Balancer поддерживает сценарии как входящих так и исходящих подключений. Load Balancer обеспечивает низкую задержку и высокую пропускную способность, а также увеличение масштаба до миллионов потоков для всех приложений, которые используют протоколы TCP и UDP.

Ниже перечислены основные сценарии, которые можно выполнить с помощью Azure Load Balancer.

- Балансировка нагрузки **[внутреннего](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** и **[внешнего](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** трафика на виртуальных машинах Azure.

- Повышение доступности за счет распределения ресурсов **[в пределах зон](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** и **[между](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** ними.

- Настройка **[исходящего подключения](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** для виртуальных машин Azure.

- Использование **[проб работоспособности](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** для мониторинга ресурсов с балансировкой нагрузки.

- Использование **[перенаправления портов](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** , чтобы получить доступ к виртуальным машинам в виртуальной сети по общедоступному IP-адресу и порту.

- Включение поддержки **[балансировки нагрузки](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Воспользуйтесь преимуществами **[метрики и диагностики](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** для Azure Load Balancer, используя **[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** .

- Службы балансировки нагрузки на **[нескольких портах, нескольких IP-адресах или обоих](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Перемещение **[внутренних](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** и **[внешних](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** ресурсов Load Balancer в регионах Azure.

- Балансировка нагрузки потоков TCP и UDP на всех портах одновременно с использованием **[Портов высокой доступности](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Цены

За использование Load Balancer (цен. категория "Стандартный") взимается плата, размер которой зависит от таких факторов:

* Число настроенных правил для балансировки нагрузки и исходящего трафика. Правила NAT для входящего трафика не учитываются в общем количестве правил.
* Объем обработанных входящих и исходящих данных независимо от правил.

Сведения о ценах на Load Balancer уровня "Стандартный" см. раздел [Цены на Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer уровня "Базовый" предоставляется бесплатно.

## <a name="sla"></a>Соглашение об уровне обслуживания

Дополнительные сведения для Load Balancer уровня "Стандартный" см. в разделе [Соглашение об уровне обслуживания для Load Balancer](https://aka.ms/lbsla).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы приступить к работе с Load Balancer, см. [Create a public Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) (Создание общедоступного Load Balancer (цен. категория "Стандартный")).

Дополнительные сведения об ограничениях и компонентах Azure Load Balancer см. в разделе [Azure Load Balancer concepts and limitations](./concepts-limitations.md) (Компоненты и ограничения Azure Load Balancer).
