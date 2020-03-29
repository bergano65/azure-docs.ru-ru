---
title: 'Azure VPN Gateway: Настройка пакетов'
description: Узнайте о функциональных возможностях захвата пакетов, которые можно использовать на VPN шлюзах.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353520"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Настройка пакетов для VPN шлюзов

Проблемы, связанные с подключением и производительностью, часто бывают сложными и требуют значительного количества времени и усилий только для того, чтобы сузить причину проблемы. Возможность захвата пакетов значительно помогает сократить время сужения сферы действия проблемы до определенных частей сети, например, является ли проблема на стороне клиента сети, стороне сети Azure или где-то поперек. После того, как проблема была сужена, гораздо эффективнее отладить и принять меры по исправлению положения.

Есть некоторые наиболее доступные инструменты для захвата пакетов. Однако получение релевантных пакетных захватов с помощью этих инструментов часто становится громоздким, особенно при работе со сценариями трафика большого объема. Возможности фильтрации, предоставляемые захватом пакетов VPN-шлюза, становятся основным дифференциатором. Вы можете использовать VPN шлюз пакет захвата в дополнение к широко доступным инструменты захвата пакетов.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Возможности фильтрации пакетов пакетов VPN

Захват ы пакетов VPN может быть запущен на шлюзе или на определенном подключении в зависимости от потребностей клиентов. Вы также можете запустить пакетные захваты на нескольких туннелях одновременно. Вы можете захватить одно- или двухнаправление трафик, трафик IKE и ESP, а также внутренние пакеты вместе с фильтрацией на VPN шлюзе.

Использование фильтра 5 tuples (подсеть источника, подсеть назначения, порт источника, порт назначения, протокол) и флаги TCP (SYN, ACK, FIN, URG, PSH, RST) полезно при изоляции проблем на высоком объеме трафика.

При запуске захвата пакета можно использовать только один вариант в свойстве.

## <a name="setup-packet-capture-using-powershell"></a>Настройка захвата пакетов с помощью PowerShell

Приведены приведенные ниже примеры команд PowerShell для запуска и остановки захвата пакетов. Для получения дополнительной информации о параметрах параметров (например, как создать фильтр), см. [document](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Начало захвата пакетов для VPN шлюза

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Дополнительный параметр **-FilterData** можно использовать для применения фильтра.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Остановить захват пакетов для VPN шлюза

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Начало захвата пакетов для подключения шлюза VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Дополнительный параметр **-FilterData** можно использовать для применения фильтра.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Остановка захвата пакетов на подключении шлюза VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Основные рекомендации

- Запуск пакетов может повлиять на производительность. Не забудьте остановить захват пакета, когда он не нужен.
- Рекомендуемая минимальная продолжительность захвата пакетов составляет 600 секунд. Более короткая продолжительность захвата пакетов может не предоставлять полных данных из-за синхронизации проблем между несколькими компонентами на пути.
- Файлы данных захвата пакетов генерируются в формате PCAP. Используйте Wireshark или другие широко доступные приложения для открытия файлов PCAP.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о VPN Шлюзе, [см.](vpn-gateway-about-vpngateways.md)
