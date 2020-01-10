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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774267"
---
В этом разделе описывается выполнение следующих операций изменения для прямого пиринга.

* Добавление соединений с прямыми пиринга
* Удаление подключений прямого пиринга
* Обновление или понижение пропускной способности для активных соединений.
* Добавить сеанс IPv4/IPv6 в активных соединениях.
* Удаление сеанса IPv4 или IPv6 на активных подключениях.

### <a name="add-direct-peering-connections"></a>Добавление соединений с прямыми пиринга

В приведенном ниже примере описывается добавление подключений к существующему прямому пиринг.

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

### <a name="remove-direct-peering-connections"></a>Удаление подключений прямого пиринга

Удаление подключения в настоящее время не поддерживается в PowerShell. Обратитесь в службу [пиринга Майкрософт](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Обновление или понижение пропускной способности для активных соединений

В приведенном ниже примере описывается добавление 10 Гбит/к существующему прямому соединению.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавить сеанс IPv4/IPv6 в активных соединениях.

В приведенном ниже примере описывается добавление сеанса IPv6 для существующего прямого подключения только с сеансом IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удаление сеанса IPv4 или IPv6 на активных подключениях.

Удаление сеанса IPv4 или IPv6 из существующего подключения в настоящее время не поддерживается в PowerShell. Обратитесь в службу [пиринга Майкрософт](mailto:peeringexperience@microsoft.com).