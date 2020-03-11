---
title: Примеры Azure CLI для виртуальной сети
description: Примеры Azure CLI для виртуальной сети.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5ce10cf37b61b0269e6c8f2279b8814d9dc4a4f9
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271205"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Примеры Azure CLI для виртуальной сети

Следующая таблица содержит ссылки на скрипты Bash, созданные с помощью команд Azure CLI:

| | |
|----|----|
| [Создание виртуальной сети для многоуровневых приложений](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Трафик к интерфейсной подсети принимается по протоколам HTTP и SSH, в то время как трафик к внутренней подсети принимается только от MySQL по порту 3306. |
| [Пиринг между двумя виртуальными сетями](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Создание и подключение двух виртуальных сетей в одном регионе. |
| [Маршрутизация трафика через виртуальный сетевой модуль](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями, а также виртуальной машиной, которая может маршрутизировать трафик между этими двумя подсетями. |
| [Фильтрация входящего и исходящего сетевого трафика виртуальной машины](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Входящий сетевой трафик в интерфейсной подсети ограничен протоколами HTTP, HTTPS и SSH. Исходящий интернет-трафик из внутренней подсети запрещен. |
|[Настройка виртуальной сети с двойным стеком (IPv4 и IPv6) и Load Balancer (цен. категория "Базовый")](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Развертывание виртуальной сети с двойным стеком (IPv4 и IPv6), двумя виртуальными машинами и Azure Load Balancer категории "Базовый" с общедоступными IP-адресами IPv4 и IPv6. |
|[Настройка виртуальной сети с двойным стеком (IPv4 и IPv6) и Load Balancer (цен. категория "Стандартный")](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Развертывает виртуальную сеть с двойным стеком (IPv4 и IPv6), двумя виртуальными машинами и Azure Load Balancer (цен. категория "Стандартный") с общедоступными IP-адресами IPv4 и IPv6. |
|[Руководство. Создание и проверка шлюза NAT (Azure CLI)](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Создайте и проверьте шлюз NAT, используя виртуальную машину в качестве источника и назначения. |