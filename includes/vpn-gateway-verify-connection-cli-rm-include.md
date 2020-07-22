---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67185117"
---
Убедиться в успешном выполнении подключения можно с помощью команды [az network vpn-connection show](/cli/azure/network/vpn-connection). В примере параметр --name — это имя подключения, которое требуется проверить. Когда подключение устанавливается, это отображено соответствующим образом (состояние "Подключение"). После установки подключения его состояние изменяется на "Подключено".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
