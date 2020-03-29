---
title: Диапазоны частных IP-адресов Azure Firewall SNAT
description: Вы можете настроить IP-адреса частных диапазонов, чтобы брандмауэр не sNAT трафик на эти IP-адреса.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064814"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Диапазоны частных IP-адресов Azure Firewall SNAT

Azure Firewall не sNAT с правилами сети, когда IP-адрес назначения находится в диапазоне частных IP-адресов [в IANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Правила приложения всегда применяются с помощью [прозрачного прокси,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) независимо от IP-адреса назначения.

Если организация использует общедоступный диапазон IP-адресов для частных сетей, Azure Firewall sNATs передает трафик на один из частных IP-адресов брандмауэра в AzureFirewallSubnet. Тем не менее, можно настроить Azure Firewall, чтобы **не** SNAT диапазон вашего общедоступного IP-адреса.

## <a name="configure-snat-private-ip-address-ranges"></a>Настройка диапазонов частных IP-адресов SNAT

Можно использовать Azure PowerShell для указания диапазона IP-адресов, который брандмауэр не будет SNAT.

### <a name="new-firewall"></a>Новый брандмауэр

Для нового брандмауэра команда Azure PowerShell:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges расширяется до текущих по умолчанию на Azure Firewall, в то время как другие диапазоны добавляются к нему.

Для получения дополнительной информации [см.](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)

### <a name="existing-firewall"></a>Существующий брандмауэр

Для настройки существующего брандмауэра используйте следующие команды Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Шаблоны

Вы можете добавить `additionalProperties` в раздел следующее:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [развертывании и настройке Брандмауэра Azure](tutorial-firewall-deploy-portal.md).