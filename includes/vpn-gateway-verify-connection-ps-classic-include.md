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
ms.openlocfilehash: 03dc6d8bb95a952a77be31f79df36a2c1ddc8ffc
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458645"
---
Чтобы проверить выполнение подключения, используйте командлет Get-AzureVNetConnection.

1. Используйте командлет из следующего примера, подставив свои значения. Если имя виртуальной сети содержит пробелы, оно должно быть заключено в кавычки.

  ```azurepowershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. После завершения работы командлета просмотрите результаты. В следующем примере показано, что подключение установлено (состояние "Подключено"), а также указан объем полученных и отправленных данных в байтах.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal