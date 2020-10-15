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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81274810"
---
Определяемые пользователем маршруты с назначением 0.0.0.0/0 и группы NSG в GatewaySubnet **не поддерживаются**. Шлюзы с такой конфигурацией будет заблокированы для создания. Для правильной работы шлюзов нужен доступ к контроллерам управления. Чтобы обеспечить доступность шлюза, в GatewaySubnet для параметра [Распространение маршрутов BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) нужно установить значение "Включено". Если этот параметр отключен, шлюз не будет работать.
