---
title: Диапазоны частных IP-адресов SNAT в брандмауэре Azure
description: Можно настроить частные диапазоны IP-адресов, чтобы брандмауэр не направлял трафик на эти IP-адреса.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064814"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Диапазоны частных IP-адресов SNAT в брандмауэре Azure

Брандмауэр Azure не поддерживает SNAT с сетевыми правилами, если IP-адрес назначения находится в диапазоне частных IP-адресов на [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Правила приложений всегда применяются с использованием [прозрачного прокси-сервера](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) независимо от IP-адреса назначения.

Если в вашей организации используется диапазон общедоступных IP-адресов для частных сетей, брандмауэр Azure Снатс трафик на один из частных IP-адресов брандмауэра в Азурефиреваллсубнет. Однако вы можете настроить брандмауэр Azure так, чтобы он **не** был ограничен диапазоном общедоступных IP-адресов.

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
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Шаблоны

В `additionalProperties` раздел можно добавить следующее:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Дальнейшие шаги

- См. дополнительные сведения о [развертывании и настройке Брандмауэра Azure](tutorial-firewall-deploy-portal.md).