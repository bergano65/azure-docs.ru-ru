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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680767"
---
В этом разделе описывается, как выполнять следующие операции модификации для прямого пиринга:

* Добавить прямые вpeering соединения.
* Удалите прямые вглядывающиеся соединения.
* Обновление или понижение пропускной способности на активных соединениях.
* Добавьте сеансы IPv4 или IPv6 на активных соединениях.
* Удалите сеансы IPv4 или IPv6 на активных соединениях.

### <a name="add-direct-peering-connections"></a>Добавление прямых пиринговых соединений

В этом примере описывается, как добавить соединения к существующему прямому пирингу.

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

Удаление соединения в настоящее время не поддерживается на PowerShell. Для получения дополнительной информации обратитесь в [грансь-наВеркинг е.](mailto:peeringexperience@microsoft.com)

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Обновление или понижение пропускной способности на активных соединениях

В этом примере описывается, как добавить 10 Гбит/с в существующее прямое соединение.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Добавление сеансов IPv4 или IPv6 на активных соединениях

В этом примере описывается, как добавить сеанс IPv6 в существующую прямую связь только с сеансом IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Удаление сеансов IPv4 или IPv6 на активных соединениях

Удаление сеанса IPv4 или IPv6 из существующего соединения в настоящее время не поддерживается на PowerShell. Для получения дополнительной информации обратитесь в [грансь-наВеркинг е.](mailto:peeringexperience@microsoft.com)