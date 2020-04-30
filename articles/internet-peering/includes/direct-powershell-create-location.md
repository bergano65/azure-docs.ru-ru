---
title: Включить имя файла
titleSuffix: Azure
description: включить файл
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680786"
---
Командлет PowerShell **Get-азпиринглокатион** возвращает список расположений пиринга с обязательным параметром `Kind`, который вы будете использовать в последующих шагах.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Расположения с прямым пирингм содержат следующие поля:
* пиринглокатион 
* Country
* пирингдбфаЦилитид
* пирингдбфаЦилитилинк
* бандвидсофферс

Проверьте наличие нужного средства пиринга, обратившись к [пирингдб](https://wwww.peeringdb.com).

В этом примере показано, как использовать Сиэтле в качестве расположения пиринга для создания прямого пиринга.

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