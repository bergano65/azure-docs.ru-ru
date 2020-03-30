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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774267"
---
В этом разделе описывается, как выполнять следующие операции модификации для прямого пиринга:

* Добавление прямых пиринговых соединений
* Удалить прямые соединения пиринга
* Обновление или понижение пропускной способности на активных соединениях.
* Добавьте сеанс IPv4/IPv6 на активных соединениях.
* Удалите сеанс IPv4/IPv6 на активных соединениях.

### <a name="add-direct-peering-connections"></a>Добавление прямых пиринговых соединений

Ниже приведенпримеры, как добавить соединения к существующему прямому пирингу

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Удалить прямые соединения пиринга

Удаление соединения в настоящее время не поддерживается на PowerShell. Обратитесь в [экспертные штаты Майкрософт](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Обновление или понижение пропускной способности на активных соединениях

Ниже приведенпримеры, как добавить 10Gbps к существующему прямому соединению.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавьте сеанс IPv4/IPv6 на активных соединениях.

Ниже приводится пример, как добавить сеанс IPv6 в существующую прямую связь только с сеансом IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удалите сеанс IPv4/IPv6 на активных соединениях.

Удаление сеанса IPv4/IPv6 из существующего соединения в настоящее время не поддерживается на PowerShell. Обратитесь в [экспертные штаты Майкрософт](mailto:peeringexperience@microsoft.com).