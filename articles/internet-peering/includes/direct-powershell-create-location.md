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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680786"
---
PowerShell cmdlet **Get-AzPeeringLocation** возвращает список вглядывающихся `Kind`мест с обязательным параметром, который вы будете использовать в более поздних шагах.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Прямые вглядывающиеся места содержат следующие поля:
* PeeringLocation 
* Country
* PeeringDBFacilityid
* PeeringDBFacilityLink
* Пропускная способностьПредложения

Проверка того, что вы присутствуете на желаемом объекте пиринга, ссылаясь на [PeeringDB](https://wwww.peeringdb.com).

В этом примере показано, как использовать Сиэтл в качестве вpeering место для создания прямого пиринга.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```