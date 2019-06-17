---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157638"
---
| **поставщик** | **Семейство устройств** | **Версия встроенного ПО** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (предварительная версия)|
|Cisco | ASA | ASA* RouteBased (IKEv2 — без BGP) для ASA до версии 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 — без BGP) для ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| Routebased v1.10x EdgeOS VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> \* Требуется: NarrowAzureTrafficSelectors (включите параметр UsePolicyBasedTrafficSelectors) и CustomAzurePolicies (IKE или IPsec).
>
