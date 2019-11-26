---
title: включение файла
description: включение файла
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485495"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Networking limits - Azure Resource Manager The following limits apply only for networking resources managed through **Azure Resource Manager** per region per subscription. Узнайте, как [просмотреть текущие данные об использовании ресурсов в соответствии с ограничениями подписки](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Недавно мы увеличили все ограничения по умолчанию до максимального уровня. If there's no maximum limit column, the resource doesn't have adjustable limits. If you had these limits increased by support in the past and don't see updated limits in the following tables, [open an online customer support request at no charge](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Ресурс | Default/maximum limit | 
| --- | --- |
| Виртуальные сети |1000 |
| Подсетей на виртуальную сеть |3000 |
| Virtual network peerings per virtual network |500 |
| [Virtual network gateways (VPN Gateways) per virtual network](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS servers per virtual network |20 |
| Private IP addresses per virtual network |65,536 |
| Private IP addresses per network interface |256 |
| Private IP addresses per virtual machine |256 |
| Public IP addresses per network interface |256 |
| Public IP addresses per virtual machine |256 |
| Параллельные потоки TCP или UDP для каждого сетевого адаптера виртуальной машины или экземпляра роли |500 000 |
| Network interface cards |65,536 |
| Группы безопасности сети |5 000 |
| Правил группы NSG на группу NSG |1000 |
| IP-адреса и диапазоны, указанные для источника или назначения в группе безопасности |4000 |
| Группы безопасности приложений |3000 |
| Группы безопасности приложений на IP-конфигурацию для каждого сетевого адаптера |20 |
| IP-конфигураций на группу безопасности приложения |4000 |
| Группы безопасности приложений, которые могут быть указаны в пределах всех правил безопасности группы безопасности сети |100 |
| User-defined route tables |200 |
| User-defined routes per route table |400 |
| Point-to-site root certificates per Azure VPN Gateway |20 |
| Элементы TAP виртуальной сети |100 |
| Число конфигураций TAP сетевых интерфейсов на TAP виртуальной сети |100 |

#### <a name="publicip-address"></a>Ограничения для общедоступных IP-адресов
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| Общедоступные IP-адреса (динамические) | 1,000 for Basic. |Обратитесь в службу поддержки. |
| Общедоступные IP-адреса (статические) | 1,000 for Basic. |Обратитесь в службу поддержки. |
| Общедоступные IP-адреса (статические) | 1,000 for Standard.|Обратитесь в службу поддержки. |
| Public IP prefix length | /28 | Обратитесь в службу поддержки. |

#### <a name="load-balancer"></a>Load balancer limits
Следующие ограничения применяются только к сетевым ресурсам, управление которыми осуществляется с помощью Azure Resource Manager для региона и на подписку. Узнайте, как [просмотреть текущие данные об использовании ресурсов в соответствии с ограничениями подписки](../articles/networking/check-usage-against-limits.md).

| Ресурс | Default/maximum limit |
| --- | --- |
| Балансировщики нагрузки | 1000 | 
| Правил на ресурс, уровень "Базовый" | 250 |
| Правил на ресурс, уровень "Стандартный" | 1500 | 
| Правил на IP-конфигурацию | 299 |
| Правил на сетевой адаптер | 300 |
| Front-end IP configurations, Basic | 200 |
| Front-end IP configurations, Standard | 600 |
| Back-end pool, Basic | 100, single availability set |
| Back-end pool, Standard | 1,000, single virtual network |
| Back-end resources per load balancer, Standard<sup>1</sup> | 150 |
| High-availability ports, Standard | 1 per internal front-end |

<sup>1</sup>The limit is up to 150 resources, in any combination of standalone virtual machine resources, availability set resources, and virtual machine scale-set resources.

#### <a name="virtual-networking-limits-classic"></a>The following limits apply only for networking resources managed through the **classic** deployment model per subscription. Узнайте, как [просмотреть текущие данные об использовании ресурсов в соответствии с ограничениями подписки](../articles/networking/check-usage-against-limits.md).

| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| Виртуальные сети |100 |100 |
| Сайты локальной сети |20 |50 |
| DNS servers per virtual network |20 |20 |
| Private IP addresses per virtual network |4096 |4096 |
| Параллельные потоки TCP или UDP для каждого сетевого адаптера виртуальной машины или экземпляра роли |500,000, up to 1,000,000 for two or more NICs. |500,000, up to 1,000,000 for two or more NICs. |
| Группы безопасности сети |200 |200 |
| Правил группы NSG на группу NSG |1000 |1000 |
| User-defined route tables |200 |200 |
| User-defined routes per route table |400 |400 |
| Общедоступные IP-адреса (динамические) |500 |500 |
| Зарезервированные общедоступные IP-адреса |500 |500 |
| Виртуальный IP-адрес на развертывание |5 |Обращение в службу поддержки |
| Private VIP (internal load balancing) per deployment |1 |1 |
| Endpoint access control lists (ACLs) |50 |50 |
