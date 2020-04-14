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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274810"
---
Маршруты с направлением 0.0.0.0/0 и NS-коды в GatewaySubnet **не поддерживаются.** Шлюзы, созданные с помощью этой конфигурации, будут заблокированы от создания. Шлюзы требуют доступа к контроллерам управления для того, чтобы функционировать должным образом. [Распространение маршрутов BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) должно быть настроено на "Enabled" на GatewaySubnet, чтобы обеспечить доступность шлюза. Если это настроено на отключение, шлюз не будет функционировать.
