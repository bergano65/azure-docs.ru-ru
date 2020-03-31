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
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838167"
---
Пользователь определил маршруты с пунктом назначения 0.0.0.0/0, а НСГ на GatewaySubnet **не поддерживаются.** Шлюзы, созданные с помощью этой конфигурации, будут заблокированы от создания. Шлюзы требуют доступа к контроллерам управления для того, чтобы функционировать должным образом.
