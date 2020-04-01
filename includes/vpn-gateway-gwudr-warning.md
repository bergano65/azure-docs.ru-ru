---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80502339"
---
Маршруты с направлением 0.0.0.0/0 и NS-коды в GatewaySubnet **не поддерживаются.** Шлюзы, созданные с помощью этой конфигурации, будут заблокированы от создания. Шлюзы требуют доступа к контроллерам управления для того, чтобы функционировать должным образом. [Распространение маршрутов BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) должно быть настроено на "Enabled" на GatewaySubnet, чтобы обеспечить доступность шлюза. Если это настроено на отключение, шлюз не будет функционировать.
