---
title: включить файл
description: включить файл
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: c63160d7514dccb0d2a9c2879db6d3fd614e1a96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75646400"
---
> [!div class="op_single_selector"]
> * [Портал Azure](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

К виртуальной машине Azure подключены один или несколько сетевых интерфейсов. Каждому такому адаптеру статически или динамически назначается один или несколько общедоступных или частных IP-адресов. Назначение нескольких IP-адресов виртуальной машине дает следующие возможности:

* Возможность размещать на одном сервере несколько веб-сайтов или служб с разными IP-адресами и SSL-сертификатами.
* возможность использовать виртуальную машину в качестве виртуального сетевого устройства, такого как брандмауэр или балансировщик нагрузки.
* Возможность добавления любых частных IP-адресов любых сетевых карт во внутренний пул Azure Load Balancer. Раньше во внутренний пул можно было добавить только основной IP-адрес для основной сетевой карты. Дополнительные сведения о балансировке нагрузки в конфигурациях с несколькими IP-адресами см. в [этой статье](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Каждому сетевому адаптеру, подключенному к виртуальной машине, присвоена одна или несколько конфигураций IP-адресов. Каждая конфигурация получает один статический или динамический частный IP-адрес. Кроме того, каждой конфигурации также может быть присвоен один ресурс общедоступного IP-адреса. Ресурсу общедоступного IP-адреса назначен один динамический или статический общедоступный IP-адрес. Дополнительные сведения об IP-адресах в Azure см. в [этой статье](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). 

Сетевой карте может быть назначено ограниченное число частных IP-адресов. Число общедоступных IP-адресов, которые можно использовать в одной подписке Azure, также ограничено. См. дополнительные сведения об [ограничениях в Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
