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
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561503"
---
Определяемые пользователем маршруты с назначением 0.0.0.0/0 и группы NSG в GatewaySubnet **не поддерживаются**. Шлюзы с такой конфигурацией будет заблокированы для создания. Для правильной работы шлюзов нужен доступ к контроллерам управления. Чтобы обеспечить доступность шлюза, в GatewaySubnet для параметра [Распространение маршрутов BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) нужно установить значение "Включено". Если этот параметр отключен, шлюз не будет работать.