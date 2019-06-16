---
title: включение файла
description: включение файла
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 0c3bc3f2995131c7777bfc48269a17fceda33192
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170770"
---
> [!div class="op_single_selector"]
> * [портал Azure](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Интерфейс командной строки Azure](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

К виртуальной машине Azure подключены один или несколько сетевых интерфейсов. Каждому такому адаптеру статически или динамически назначается один или несколько общедоступных или частных IP-адресов. Назначение нескольких IP-адресов виртуальной машине дает следующие возможности:

* Возможность размещать на одном сервере несколько веб-сайтов или служб с разными IP-адресами и SSL-сертификатами.
* возможность использовать виртуальную машину в качестве виртуального сетевого устройства, такого как брандмауэр или балансировщик нагрузки.
* Возможность добавления любых частных IP-адресов любых сетевых карт во внутренний пул Azure Load Balancer. Раньше во внутренний пул можно было добавить только основной IP-адрес для основной сетевой карты. Дополнительные сведения о балансировке нагрузки в конфигурациях с несколькими IP-адресами см. в [этой статье](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Каждому сетевому адаптеру, подключенному к виртуальной машине, присвоена одна или несколько конфигураций IP-адресов. Каждая конфигурация получает один статический или динамический частный IP-адрес. Кроме того, каждой конфигурации также может быть присвоен один ресурс общедоступного IP-адреса. Ресурсу общедоступного IP-адреса назначен один динамический или статический общедоступный IP-адрес. Дополнительные сведения об IP-адресах в Azure см. в [этой статье](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). 

Сетевой карте может быть назначено ограниченное число частных IP-адресов. Число общедоступных IP-адресов, которые можно использовать в одной подписке Azure, также ограничено. См. дополнительные сведения об [ограничениях в Azure](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
