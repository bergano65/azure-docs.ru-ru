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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774293"
---
PowerShell cmdlet **Get-AzPeeringLocation** возвращает список вглядывающихся мест с обязательным параметром, `Kind`который вы будете использовать в последующих шагах:

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

Ниже приведен пример, который показывает, как использовать Сиэтл в качестве внимательного местоположения для создания прямого пиринга:

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