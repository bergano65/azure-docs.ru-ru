---
title: Образцы Azure PowerShell - Сеть
description: Примеры сценариев Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: ca6ac145db0536d3cf7e5bcc72a58d72101ab12a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459123"
---
# <a name="azure-powershell-samples-for-networking"></a>Примеры Azure PowerShell для работы с сетями

В таблице ниже приведены ссылки на скрипты, созданные с помощью Azure PowerShell.

| | |
|-|-|
|**Связь между ресурсами Azure**||
| [Создание виртуальной сети для многоуровневых приложений](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Трафик к интерфейсной подсети принимается по протоколу HTTP, в то время как трафик к внутренней подсети принимается только от SQL через порт 1433. |
| [Пиринг между двумя виртуальными сетями](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание и подключение двух виртуальных сетей в одном регионе. |
| [Маршрутизация трафика через виртуальный сетевой модуль](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание виртуальной сети с интерфейсной и внутренней подсетями, а также виртуальной машиной, которая может маршрутизировать трафик между этими двумя подсетями. |
| [Фильтрация входящего и исходящего сетевого трафика виртуальной машины](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создание виртуальной сети с интерфейсной и внутренней подсетями. Входящий сетевой трафик в интерфейсной подсети ограничен протоколами HTTP и HTTPS. Исходящий трафик в Интернет из внутренней подсети запрещен. |
|**Балансировка нагрузки и направление движения**||
| [Балансировка трафика на виртуальных машинах для обеспечения высокой доступности](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Направление трафика через несколько регионов для обеспечения высокого уровня доступности приложений](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Создание двух планов службы приложений, двух веб-приложений, а также профиля и двух конечных точек диспетчера трафика. |
| | |
