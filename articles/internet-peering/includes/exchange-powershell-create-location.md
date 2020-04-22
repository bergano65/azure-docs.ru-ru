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
ms.openlocfilehash: 2ebf5d574b4d185953ab0f7984648c440d6f107e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678620"
---
PowerShell cmdlet **Get-AzPeeringLocation** возвращает список вглядывающихся `Kind`мест с обязательным параметром, который вы будете использовать в более поздних шагах.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Места для обмена вглядывающимися содержат следующие поля:
* ExchangeName
* PeeringLocation
* Country
* PeeringDBFacilityid
* PeeringDBFacilityLink
* Адрес MicrosoftIPv4
* Адрес MicrosoftIPv6

Проверка того, что вы присутствуете на желаемом объекте пиринга, ссылаясь на [PeeringDB](https://wwww.peeringdb.com).

В этом примере показано, как использовать Сиэтл в качестве вpeering место для создания пиринга.

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