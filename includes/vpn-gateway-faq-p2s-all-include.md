---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ec684e5e6fa2ef8e9ed30be49f59e8aa7ef3a28b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117095"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Сколько конечных точек VPN-клиента можно настроить в конфигурации "точка — сеть"?

Это зависит от SKU шлюза. Дополнительные сведения о количестве поддерживаемых подключений см. в разделе [SKU шлюзов](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>Какие клиентские операционные системы можно использовать для подключения типа "точка — сеть"?

Поддерживаются следующие клиентские операционные системы:

* Windows 7 (32-разрядная и 64-разрядная версии)
* Windows Server 2008 R2 (только 64-разрядная версия)
* Windows 8.1 (32-разрядная и 64-разрядная версии)
* Windows Server 2012 (только 64-разрядная версия)
* Windows Server 2012 R2 (только 64-разрядная версия)
* Windows Server 2016 (только 64-разрядная версия)
* Windows 10
* Mac OS X версии 10.11 или более поздней
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Можно ли просматривать прокси-серверы и брандмауэры с использованием возможности "точка — сеть"?

Azure поддерживает три типа VPN-опций Point-to-site:

* По протоколу SSTP (Secure Sockets Tunneling Protocol). SSTP — это решение на основе SSL на базе Microsoft, которое может проникать в брандмауэры, так как большинство брандмауэров открывают исходящий порт TCP, который использует 443 SSL.

* Openvpn. OpenVPN — это решение на основе SSL, которое может проникать в брандмауэры, так как большинство брандмауэров открывают исходящий порт TCP, который использует 443 SSL.

* По протоколу IKEv2 для VPN. IKEv2 VPN - это стандартное решение IPsec VPN, которое использует исходящие порты UDP 500 и 4500 и IP-протокол No. 50. Эти порты не всегда открываются в брандмауэре, поэтому есть вероятность, что IKEv2 для VPN не сможет пройти через прокси-серверы и брандмауэры.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Если перезапустить клиентский компьютер, настроенный для подключения "точка — сеть", произойдет ли автоматическое переподключение VPN?

По умолчанию клиентский компьютер не восстанавливает VPN-подключение автоматически.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Поддерживает ли подключение "точка — сеть" автоматическое повторное подключение и DDNS для VPN-клиентов?

В конфигурациях VPN "точка — сеть" в настоящее время не поддерживаются автоматическое повторное подключение и DDNS.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Могут ли сосуществовать в одной виртуальной сети конфигурации "сеть — сеть" и "точка — сеть"?

Да. Для модели развертывания с помощью Resource Manager требуется VPN-шлюз с маршрутизацией на основе маршрутов. В классической модели развертывания требуется динамический шлюз. Подключения типа "точка — сеть" для VPN-шлюзов со статической маршрутизацией или маршрутизацией на основе политик не поддерживаются.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>Могу ли я настроить клиента от точки к сайту для подключения к нескольким виртуальным сетевым шлюзам одновременно?

В зависимости от используемого программного обеспечения VPN Client, вы можете подключиться к нескольким виртуальной сети шлюзов при условии, что виртуальные сети подключены к не имеют противоречивых адресных пространств между ними или сети с клиентом подключается от.  В то время как клиент Azure VPN поддерживает множество VPN-соединений, только одно соединение может быть подключено в любой момент времени.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Можно ли настроить клиент "точка — сеть" для подключения к нескольким виртуальным сетям одновременно?

Да, подключение от «точки к месту» к виртуальному сетевому шлюзу, развернутому в VNet, который используется с другими VNets, может иметь доступ к другим заглянуV-сетям.  При условии, что заглянул VNets используют UseRemoteGateway / AllowGatewayTransit функции, точка к месту клиент сможет подключиться к этим заглянул VNets.  Для получения дополнительной информации, пожалуйста, обратитесь к [этой](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md) статье.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>На какую пропускную способность можно рассчитывать в конфигурациях подключения "сеть — сеть" и "точка — сеть"?

Сложно поддерживать конкретную пропускную способность для туннелей VPN. IPsec и SSTP — это надежно зашифрованные протоколы VPN. Пропускная способность также зависит от задержки и пропускной способности между локальной сетью и Интернетом. Для VPN-шлюза, который используется только для VPN-подключений типа "точка — сеть" по протоколу IKEv2, общая пропускная способность зависит от номера SKU шлюза. Дополнительные сведения о пропускной способности см. в статье о [номерах SKU шлюзов](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Можно ли использовать для подключения "точка — сеть" любой программный VPN-клиент, поддерживающий SSTP и (или) IKEv2?

Нет. Вы можете использовать только собственный VPN-клиент для SSTP в Windows и собственный VPN-клиент для IKEv2 в Mac. Тем не менее, вы можете использовать клиента OpenVPN на всех платформах для подключения к протоколу OpenVPN. См. список поддерживаемых клиентских операционных систем.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure поддерживает протокол IKEv2 для VPN в Windows?

IKEv2 поддерживается в Windows 10 и Server 2016. Однако для использования IKEv2 необходимо установить обновления и задать значение раздела реестра локально. Версии ОС до Windows 10 не поддерживаются и могут использовать только SSTP или **OpenVPN® протокол.**

Чтобы подготовить Windows 10 или Server 2016 IKEv2:

1. Установите обновление.

   | Версия ОС | Дата | Номер или ссылка |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 версии 1607 | 17 января 2018 г. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 версии 1703 | 17 января 2018 г. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 версии 1709 | 22 марта 2018 г. | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Установите значение раздела реестра. Создайте или задайте для ключа REG_DWORD "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload" в реестре значение 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Что произойдет, если настроить и SSTP, и IKEv2 для подключений VPN типа "точка — сеть"?

При настройке SSTP и IKEv2 в смешанной среде (состоящей из устройств Windows и Mac) VPN клиент Windows будет сначала пытаться подключиться к туннелю IKEv2, но, если не удастся установить соединение, подключится к SSTP. MacOSX подключится только через IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Какие еще платформы, кроме Windows и Mac, Azure поддерживает для VPN-подключений типа "точка — сеть"?

Azure поддерживает VPN-подключения "точка — сеть" в Windows, Mac и Linux.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>У меня уже развернут VPN-шлюз Azure. Можно ли включить на нем RADIUS и/или IKEv2 для VPN?

Да, эти новые функции можно включить для уже развернутых шлюзов с помощью Powershell или портала Azure при условии, что используемый шлюз SKU поддерживает RADIUS и/или IKEv2. Например, VPN-шлюз со SKU "Базовый" не поддерживает RADIUS или IKEv2.

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>Как удалить конфигурацию соединения P2S?

Конфигурация P2S может быть удалена с помощью Azure CLI и PowerShell с помощью следующих команд:

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
