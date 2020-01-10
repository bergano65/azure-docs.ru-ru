---
title: Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell
titleSuffix: Azure
description: Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775021"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell

В этой статье описывается, как преобразовать существующий прямой пиринг пиринга в ресурс Azure с помощью командлетов PowerShell.

При желании вы можете выполнить инструкции с помощью [портала](howto-legacy-direct-portal.md).

## <a name="before-you-begin"></a>Перед началом работы
* Перед началом настройки проверьте [Предварительные требования](prerequisites.md) и [Пошаговое руководство по непосредственному пирингу](walkthrough-direct-all.md) .

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Преобразование устаревшего прямого пиринга в ресурс Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Получение устаревшего прямого пиринга для преобразования
Ниже приведен пример получения устаревшего прямого пиринга в расположении пиринга в Сиэтле.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Ниже приведен пример ответа.
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-legacy-direct-peering"></a>Преобразование устаревшего прямого пиринга

&nbsp;
> [!IMPORTANT]
> Обратите внимание, что при преобразовании устаревшего пиринга в ресурс Azure изменения не поддерживаются. &nbsp;

Используйте приведенную ниже команду для преобразования устаревшего прямого пиринга в ресурс Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Ниже приведен пример ответа.

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md).
