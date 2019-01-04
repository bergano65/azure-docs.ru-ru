---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 267234cb9ecea1dc097f13739bf98ee11206ad06
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444326"
---
Убедиться в успешном выполнении подключения можно с помощью командлета Get-AzureRmVirtualNetworkGatewayConnection с параметром -Debug или без него. 

1. Используйте командлет из следующего примера, подставив свои значения. При появлении запроса выберите "A", чтобы выполнить команду All (Все). В примере параметр --name — это имя подключения, которое требуется проверить.

   ```azurepowershell-interactive
   Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. После завершения работы командлета просмотрите результаты. В следующем примере показано, что подключение установлено (состояние "Подключено"), а также указан объем полученных и отправленных данных в байтах.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```