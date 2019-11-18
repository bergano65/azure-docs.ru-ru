---
title: 'VPN-шлюз Azure: Настройка записи пакетов'
description: Сведения о функциях записи пакетов, которые можно использовать на VPN-шлюзах.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 41c36d302605bb619899131a8ace649b0f1439b2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151855"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Настройка записи пакетов для VPN-шлюзов

Проблемы с подключением и производительностью часто бывают довольно сложными и занимают значительное время и усилия, чтобы уменьшить причину проблемы. Возможность захвата пакетов существенно помогает сократить время, ограничивающее область действия до определенных частей сети, например, находится ли проблема на клиентской стороне сети, в сети Azure или в другом месте. После сокращения проблемы гораздо эффективнее выполнять отладку и выполнять действия по устранению неполадок.

Существует несколько широко доступных средств для записи пакетов. Однако получение релевантных данных о пакетах с помощью этих средств часто является довольно громоздким, особенно при работе с сценариями с большим объемом трафика. Возможности фильтрации, обеспечиваемые записью пакетов VPN-шлюза, становятся основными отличиями. В дополнение к распространенным средствам записи пакетов можно использовать запись пакетов VPN-шлюза.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Возможности фильтрации записи пакетов VPN-шлюза

Записи пакетов VPN-шлюза могут выполняться на шлюзе или в определенном подключении в зависимости от потребностей клиентов. Кроме того, можно выполнять запись пакетов одновременно для нескольких туннелей. Вы можете записывать трафик с одним или двунаправленным направлением, трафик IKE и ESP, а также внутренние пакеты с фильтрацией на VPN-шлюзе.

Использование пяти фильтров кортежей (исходная подсеть, конечная подсеть, порт источника, порт назначения, протокол) и флаги TCP (SYN, ACK, FIN, УРГ, КОМАНДНОМ PSH, RST) полезна при изоляции проблем с большим объемом трафика.

## <a name="setup-packet-capture-using-powershell"></a>Запись пакетов установки с помощью PowerShell

Примеры команд PowerShell для запуска и отмены записи пакетов см. в приведенных ниже примерах. Дополнительные сведения о параметрах параметров (например, о создании фильтров) см. в этом [документе](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)PowerShell.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Запуск записи пакетов для VPN-шлюза

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Необязательный параметр **-FilterData** можно использовать для применения фильтров.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Завершение записи пакетов для VPN-шлюза

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Запуск записи пакетов для подключения VPN-шлюза

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Необязательный параметр **-FilterData** можно использовать для применения фильтров.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Прерывать запись пакетов при подключении к VPN-шлюзу

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Основные рекомендации

- Выполнение записи пакетов может повлиять на производительность. Не забудьте прерывать запись пакетов, если она не требуется.
- Рекомендуемая минимальная длительность записи пакетов составляет 600 секунд. Наличие более короткой длительности записи пакетов может не предоставлять полные данные из-за синхронизации проблем между несколькими компонентами по пути.
- Файлы данных записи пакетов создаются в форматах ПКАП или ETL. Для понимания данных может потребоваться анализатор Netmon.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о VPN-шлюзе см. в статье [о VPN-шлюзе](vpn-gateway-about-vpngateways.md) .