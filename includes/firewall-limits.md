---
title: включение файла
description: включение файла
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 2/4/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 9097f2b70a80431cf3302555ad2c835338cf8d8e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55735948"
---
| Ресурс | Ограничение по умолчанию |
| --- | --- |
| Обработанные данные |1000 ТБ/брандмауэр/месяц <sup>1</sup> |
|Правила|10 000 — объединенные правила всех типов|
|Глобальный пиринг|Не поддерживается. В каждом регионе необходимо иметь хотя бы один развернутый брандмауэр.|
|Минимальный размер AzureFirewallSubnet |/26|
|Диапазон портов в правилах сети и приложений|0–64 000. Ведутся работы по расширению этого диапазона.|
|


<sup>1</sup> Если требуется увеличить предельные значения ограничений, обратитесь в службу поддержки Azure.
