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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774423"
---
Для пиринга Exchange поддерживаются следующие операции изменения
1. Добавление подключений пиринга Exchange
1. Удаление подключений пиринга Exchange
1. Добавить сеанс IPv4/IPv6 в активных соединениях.
1. Удаление сеанса IPv4 или IPv6 на активных подключениях.


### <a name="add-exchange-peering-connections"></a>Добавление подключений пиринга Exchange

В приведенном ниже примере описывается добавление подключений к существующему пирингу Exchange.

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

### <a name="remove-exchange-peering-connections"></a>Удаление подключений пиринга Exchange

Ниже приведен пример, в котором описывается удаление подключений к существующему пирингу Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Просмотрите все подключения и выберите подключение, которое нужно удалить. 

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

В приведенной ниже команде вместо 0 введите номер индекса для соединения, которое требуется удалить.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавить сеанс IPv4/IPv6 на активных подключениях

В приведенном ниже примере описывается добавление сеанса IPv6 к существующему соединению Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удалить сеанс IPv4 или IPv6 на активных подключениях

Удаление сеанса IPv4 или IPv6 из существующего подключения в настоящее время не поддерживается в PowerShell. Обратитесь в службу [пиринга Майкрософт](mailto:peeringexperience@microsoft.com).