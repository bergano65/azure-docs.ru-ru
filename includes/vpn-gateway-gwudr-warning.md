---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81274810"
---
Определяемые пользователем маршруты с назначением 0.0.0.0/0 и группы безопасности сети в GatewaySubnet **не поддерживаются**. Создание шлюзов, созданных с этой конфигурацией, будет заблокировано. Для правильной работы шлюзов требуется доступ к контроллерам управления. Для обеспечения доступности шлюза на GatewaySubnet должно быть установлено значение "включено" для [распространения маршрутов BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) . Если задано значение отключено, шлюз не будет работать.
