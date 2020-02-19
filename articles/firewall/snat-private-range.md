---
title: Диапазоны частных IP-адресов SNAT в брандмауэре Azure
description: Можно настроить частные диапазоны IP-адресов, чтобы брандмауэр не направлял трафик на эти IP-адреса.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444463"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Диапазоны частных IP-адресов SNAT в брандмауэре Azure

Брандмауэр Azure не поддерживает SNAT, если IP-адрес назначения находится в диапазоне частных IP-адресов на [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Если для частных сетей в организации используются общедоступные IP-адреса, брандмауэр Azure будет использовать SNAT трафика для одного из своих частных IP-адресов AzureFirewallSubnet. Однако вы можете настроить брандмауэр Azure так, чтобы он **не** был ограничен диапазоном общедоступных IP-адресов.

## <a name="configure-snat-private-ip-address-ranges"></a>Настройка диапазонов частных IP-адресов SNAT

Вы можете использовать Azure PowerShell, чтобы указать диапазон IP-адресов, который брандмауэр не сможет SNAT.

### <a name="new-firewall"></a>Новый брандмауэр

Для нового брандмауэра используется команда Azure PowerShell:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> Ианаприватеранжес расширяется до текущих значений по умолчанию в брандмауэре Azure, в то время как к нему добавляются другие диапазоны.

Дополнительные сведения см. в разделе [New-азфиревалл](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Существующий брандмауэр

Чтобы настроить существующий брандмауэр, используйте следующие Azure PowerShell команды:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Шаблоны

В раздел `additionalProperties` можно добавить следующее:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [развертывании и настройке Брандмауэра Azure](tutorial-firewall-deploy-portal.md).