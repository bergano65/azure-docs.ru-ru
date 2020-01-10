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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774397"
---
Командлет PowerShell **Get-азпиринглокатион** возвращает список расположений пиринга с обязательным параметром `Kind`, который вы будете использовать в последующих шагах:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Расположения пиринга Exchange содержат следующие поля:
* ексчанженаме
* пиринглокатион
* Страна
* пирингдбфаЦилитид
* пирингдбфаЦилитилинк
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Проверьте наличие нужного средства пиринга, обратившись к [пирингдб](https://w www.peeringdb.com).

Ниже приведен пример, демонстрирующий использование Сиэтле в качестве расположения пиринга для создания пиринга.

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