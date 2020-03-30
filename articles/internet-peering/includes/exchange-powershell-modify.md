---
title: включить файл
titleSuffix: Azure
description: включить файл
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774423"
---
После операции модификации поддерживаются для пиринга Exchange
1. Добавление внравных соединений Exchange
1. Удалить соединения пиринга Exchange
1. Добавьте сеанс IPv4/IPv6 на активных соединениях.
1. Удалите сеанс IPv4/IPv6 на активных соединениях.


### <a name="add-exchange-peering-connections"></a>Добавление внравных соединений Exchange

Ниже приведенпримеры, как добавить соединения к существующему пирингу Exchange

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Удалить соединения пиринга Exchange

Ниже приведенпримеры, как удалить соединения с существующими вглядывающимися Exchange

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Просмотр всех подключений и выберите соединение, которые вы хотите удалить. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

В приведенной ниже команде вместо 0 введите номер индекса для соединения, который вы хотите удалить.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавление сеанса IPv4/IPv6 на активных соединениях

Ниже приведенпример, как добавить сеанс IPv6 в существующее обменное соединение.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удалить сеанс IPv4/IPv6 на активных соединениях

Удаление сеанса IPv4/IPv6 из существующего соединения в настоящее время не поддерживается на PowerShell. Обратитесь в [экспертные штаты Майкрософт](mailto:peeringexperience@microsoft.com).