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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774397"
---
PowerShell cmdlet **Get-AzPeeringLocation** возвращает список вглядывающихся мест с обязательным параметром, `Kind`который вы будете использовать в последующих шагах:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Местоположения exchange peering содержат следующие поля:
* ExchangeName
* PeeringLocation
* Country
* PeeringDBFacilityid
* PeeringDBFacilityLink
* Адрес MicrosoftIPv4
* Адрес MicrosoftIPv6

Проверка того, что вы присутствуете на желаемом объекте пиринга, ссылаясь на [PeeringDB](https://wwww.peeringdb.com).

Ниже приведен пример, который показывает, как использовать Сиэтл в качестве вглядывающей сядутки для создания пиринга:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```