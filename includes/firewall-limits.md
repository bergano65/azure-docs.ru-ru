---
title: включение файла
description: включение файла
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/14/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 3e6bde63c5f759ec711b243fc25fa0ed8bde634a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58016465"
---
| Ресурс | Ограничение по умолчанию |
| --- | --- |
| Пропускная способность |30 Гбит/с<sup>1</sup> |
|Правила|10 000, все правила в сочетании типов.|
|Глобальный пиринг|Не поддерживается. В каждом регионе необходимо иметь хотя бы один развернутый брандмауэр.|
|Минимальный размер AzureFirewallSubnet |/26|
|Диапазон портов в правилах сети и приложений|0–64 000. Ведутся работы по расширению этого диапазона.|
|


<sup>1</sup>Если вам нужно увеличить эти предельные значения, обратитесь в службу поддержки Azure.
