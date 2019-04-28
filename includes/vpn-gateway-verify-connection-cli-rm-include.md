---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411819"
---
Убедиться в успешном выполнении подключения можно с помощью команды [az network vpn-connection show](/cli/azure/network/vpn-connection). В примере параметр --name — это имя подключения, которое требуется проверить. Когда подключение устанавливается, это отображено соответствующим образом (состояние "Подключение"). После установки подключения его состояние изменяется на "Подключено".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
