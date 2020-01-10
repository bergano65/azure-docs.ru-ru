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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774293"
---
Командлет PowerShell **Get-азпиринглокатион** возвращает список расположений пиринга с обязательным параметром `Kind`, который вы будете использовать в последующих шагах:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Расположения с прямым пирингм содержат следующие поля:
* пиринглокатион 
* Страна
* пирингдбфаЦилитид
* пирингдбфаЦилитилинк
* бандвидсофферс

Проверьте наличие нужного средства пиринга, обратившись к [пирингдб](https://w www.peeringdb.com).

Ниже приведен пример, демонстрирующий использование Сиэтле в качестве расположения пиринга для создания прямого пиринга.

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