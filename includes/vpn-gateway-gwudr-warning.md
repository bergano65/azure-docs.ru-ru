---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838167"
---
Определяемые пользователем маршруты с назначением 0.0.0.0/0 и группы безопасности сети в GatewaySubnet **не поддерживаются**. Создание шлюзов, созданных с этой конфигурацией, будет заблокировано. Для правильной работы шлюзов требуется доступ к контроллерам управления.
