---
title: 'Azure ExpressRoute: о шифровании'
description: Узнайте о шифровании ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461469"
---
# <a name="expressroute-encryption"></a>Шифрование ExpressRoute
 
ExpressRoute поддерживает несколько технологий шифрования для обеспечения конфиденциальности и целостности передачи данных между вашей сетью и сетью Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Шифрование по точкам MACsec часто задаваемые вопросы
MACsec является [стандартом IEEE.](https://1.ieee802.org/security/802-1ae/) Он шифрует данные на уровне управления доступом к СМИ (MAC) или Network Layer 2. Вы можете использовать MACsec для шифрования физических связей между вашими сетевыми устройствами и сетевыми устройствами Майкрософт при подключении к Microsoft через [ExpressRoute Direct.](expressroute-erdirect-about.md) MACsec отключен в портах ExpressRoute Direct по умолчанию. Вы приносите свой собственный ключ MACsec для шифрования и храните его в [Azure Key Vault.](../key-vault/general/overview.md) Вы сами решаете, когда следует повернуть ключ. Смотрите другие часто задаваемые вопросы ниже.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Могу ли я включить MACsec на моей трассе ExpressRoute, подготовленной провайдером ExpressRoute?
Нет. MACsec шифрует весь трафик на физической связи с ключом, принадлежащим одному объекту (т.е. клиенту). Таким образом, он доступен только на ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Могу ли я зашифровать некоторые схемы ExpressRoute в портах ExpressRoute Direct и оставить другие схемы в тех же портах незашифрованными? 
Нет. После включения MACsec весь сетевой трафик управления, например, трафик данных BGP и трафик данных клиентов шифруются. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Когда я включу/отключаю MACsec или новерую ключ MACsec, моя предприимчивая сеть потеряет подключение к Microsoft через ExpressRoute?
Да. Для конфигурации MACsec мы поддерживаем только предобщий ключевой режим. Это означает, что вам необходимо обновить ключ как на устройствах, так и на устройстве Майкрософт (через наш API). Это изменение не является атомным, так что вы потеряете связь, когда есть ключевое несоответствие между двумя сторонами. Мы настоятельно рекомендуем запланировать окно обслуживания для изменения конфигурации. Чтобы свести к минимуму время простоя, мы предлагаем вам обновить конфигурацию на одном из ссылок ExpressRoute Direct в то время, после переключения сетевого трафика на другую ссылку.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Будет ли трафик продолжать течь, если есть несоответствие в ключе MACsec между моими устройствами и Microsoft?
Нет. Если MACsec настроен и происходит несоответствие ключей, вы теряете подключение к microsoft. Другими словами, мы не вернемся к незашифрованным соединениям, разоблачая ваши данные. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Будет ли включение MACsec на ExpressRoute Прямая ухудшить производительность сети?
Шифрование и расшифровка MACsec происходит в оборудовании на маршрутизаторах, которые мы используем. С нашей стороны это не влияет на производительность. Тем не менее, вы должны проверить с поставщиком сети для устройств, которые вы используете и посмотреть, если MACsec имеет какие-либо последствия производительности.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>какие шифровальщики поддерживаются для шифрования?
Мы поддерживаем AES128 и AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Сквозное шифрование с помощью часто задаваемых вопросов IPsec
IPsec является [стандартом IETF.](https://tools.ietf.org/html/rfc6071) Он шифрует данные на уровне Интернет-протокола (IP) или Network Layer 3. Для шифрования сквозного соединения между вашей предварительной сетью и виртуальной сетью (VNET) на Azure можно использовать IPsec. Смотрите другие часто задаваемые вопросы ниже.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Могу ли я включить IPsec в дополнение к MACsec на моих прямых портах ExpressRoute?
Да. MACsec обеспечивает физические связи между вами и корпорацией Майкрософт. IPsec обеспечивает сквозное соединение между вами и вашими виртуальными сетями в Azure. Вы можете включить их самостоятельно. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Могу ли я использовать VPN шлюз Azure для настройки туннеля IPsec между моей предварительной сетью и виртуальной сетью Azure?
Да. Вы можете настроить этот туннель IPsec над Microsoft Peering вашей схемы ExpressRoute. Следуйте [нашему руководству по конфигурации](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Могу ли я использовать VPN шлюз Azure для настройки туннеля IPsec над Azure Private Peering?
Если вы принимаете Azure Virtual WAN, вы можете выполнить [эти действия,](../virtual-wan/vpn-over-expressroute.md) чтобы зашифровать сквозное соединение. Если у вас есть регулярный Azure VNET, вы можете развернуть сторонний VPN шлюз в VNET и установить туннель IPsec между ним и вашим vpn шлюзом.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Какова пропускная плата, которая я получу после включения IPsec в моем подключении ExpressRoute?
Если используется VPN шлюз Azure, проверьте [номера производительности здесь.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Если используется сторонний VPN шлюз, обратитесь к поставщику за номерами производительности.

## <a name="next-steps"></a>Следующие шаги
Дополнительную информацию о конфигурации MACsec можно найти в [Configure MACsec.](expressroute-howto-macsec.md)

Дополнительную информацию о конфигурации IPsec можно найти в [Configure IPsec.](site-to-site-vpn-over-microsoft-peering.md)
