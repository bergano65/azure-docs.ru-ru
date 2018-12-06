---
title: включение файла
description: включение файла
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440394"
---
| Ресурс | Ограничение по умолчанию |
| --- | --- |
| Обработанные данные |1000 ТБ/брандмауэр/месяц <sup>1</sup> |
|Правила|10 000 — объединенные правила всех типов|
|Глобальный пиринг|Не поддерживается. В каждом регионе необходимо иметь хотя бы один развернутый брандмауэр.|
|Максимальное число портов в одном сетевом правиле|15<br>Диапазон (например, 2–10) считается как два порта.
|Минимальный размер AzureFirewallSubnet |/26


<sup>1</sup> Если требуется увеличить предельные значения ограничений, обратитесь в службу поддержки Azure.
