---
title: 'Azure ExpressRoute: сведения о шифровании'
description: Сведения о шифровании ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: cherylmc
ms.openlocfilehash: 77755ab6bdbb3c1e6416475f5066b5dd463eb7f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82838762"
---
# <a name="expressroute-encryption"></a>Шифрование ExpressRoute
 
ExpressRoute поддерживает несколько технологий шифрования для обеспечения конфиденциальности и целостности данных, проходящих между сетью и сетью Майкрософт.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Шифрование "точка — точка" с помощью Максек часто задаваемых вопросов
Максек является [стандартом IEEE](https://1.ieee802.org/security/802-1ae/). Он шифрует данные на уровне управления доступом к носителю (MAC) или в сетевом уровне 2. Максек можно использовать для шифрования физических связей между сетевыми устройствами и сетевыми устройствами Майкрософт при подключении к Майкрософт через [ExpressRoute Direct](expressroute-erdirect-about.md). По умолчанию Максек отключен на портах ExpressRoute Direct. Вы перенесете собственный ключ Максек для шифрования и храните его в [Azure Key Vault](../key-vault/general/overview.md). Вы решаете, когда следует поворачивать ключ. См. другие вопросы и ответы ниже.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Можно ли включить Максек в канале ExpressRoute, подготовленном поставщиком ExpressRoute?
Нет. Максек шифрует весь трафик на физической связи с ключом, принадлежащим одной сущности (т. е. заказчику). Поэтому он доступен только в ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Можно ли зашифровать некоторые каналы ExpressRoute на своих прямых портах ExpressRoute и оставить другие цепи на одних и тех же портах без шифрования? 
Нет. Как только Максек включает весь трафик управления сетью, например, трафик BGP и трафик данных клиента шифруются. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>При включении или отключении ключа Максек или Update Максек локальная сеть теряет подключение к Майкрософт через ExpressRoute?
Да. Для конфигурации Максек поддерживается только режим предварительного общего ключа. Это означает, что необходимо обновить ключ как на устройствах, так и в корпорации Майкрософт (через наш API). Это изменение не является атомарным, поэтому подключение будет потеряно при несовпадении ключей между двумя сторонами. Настоятельно рекомендуется запланировать настройку периода обслуживания для изменения конфигурации. Чтобы сократить время простоя, мы рекомендуем обновить конфигурацию по одной ссылке ExpressRoute Direct за раз после переключения сетевого трафика на другую ссылку.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Пройдет ли трафик в случае несоответствия в Максек Key между моими устройствами и корпорацией Майкрософт?
Нет. Если Максек настроен и обнаружено несовпадение ключей, подключение к Майкрософт теряется. Другими словами, мы не будем возвращаться к незашифрованному соединению, предоставляя данные. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Включает ли Максек в ExpressRoute прямую производительность сети?
Шифрование и расшифровка Максек происходят в оборудовании на используемых маршрутизаторах. На наши стороны не влияет на производительность. Тем не менее следует обратиться к поставщику сети для устройств, которые вы используете, и узнать, не имеет ли Максек производительность.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>Какие комплекты шифров поддерживаются для шифрования?
Поддерживается только версия [расширенной нумерации пакетов](https://1.ieee802.org/security/802-1aebw/) AES128 и AES256. Кроме того, отключите [идентификатор Secure Channel (SCI)](https://en.wikipedia.org/wiki/IEEE_802.1AE) в конфигурации максек на устройстве. 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Полное шифрование по протоколу IPsec: вопросы и ответы
Протокол IPsec является [стандартом IETF](https://tools.ietf.org/html/rfc6071). Он шифрует данные на уровне протокола Интернета (IP) или уровня сети 3. Протокол IPsec можно использовать для шифрования сквозного подключения между локальной сетью и виртуальной сетью в Azure. См. другие вопросы и ответы ниже.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Можно ли включить IPsec в дополнение к Максек на моих портах ExpressRoute Direct?
Да. Максек обеспечивает защиту физических подключений между вами и корпорацией Майкрософт. IPsec обеспечивает безопасность сквозного подключения между вами и виртуальными сетями в Azure. Их можно включить независимо. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Можно ли использовать VPN-шлюз Azure для настройки туннеля IPsec между локальной сетью и виртуальной сетью Azure?
Да. Вы можете настроить этот туннель IPsec через пиринг Майкрософт канала ExpressRoute. Выполните [инструкции по настройке](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Можно ли использовать VPN-шлюз Azure для настройки туннеля IPsec через частный пиринг Azure?
При внедрении виртуальной глобальной сети Azure можно выполнить следующие [действия](../virtual-wan/vpn-over-expressroute.md) , чтобы зашифровать сквозное подключение. Если у вас есть обычная виртуальная сеть Azure, вы можете развернуть сторонний VPN-шлюз в виртуальной сети и создать туннель IPsec между ним и локальным VPN-шлюзом.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Какова пропускная способность, которую я получаю после включения IPsec для подключения ExpressRoute?
Если используется VPN-шлюз Azure, проверьте [номера производительности здесь](../vpn-gateway/vpn-gateway-about-vpngateways.md). Если используется сторонний VPN-шлюз, обратитесь к поставщику за номерами производительности.

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о конфигурации Максек см. в разделе [Configure максек](expressroute-howto-macsec.md) .

Дополнительные сведения о конфигурации IPsec см. в разделе [Настройка IPSec](site-to-site-vpn-over-microsoft-peering.md) .
