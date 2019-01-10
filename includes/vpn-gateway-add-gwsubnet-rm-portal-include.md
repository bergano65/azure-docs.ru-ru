---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444338"
---
1. На [портале Azure](http://portal.azure.com) выберите виртуальную сеть Resource Manager, для которой необходимо создать шлюз.

2. На странице виртуальной сети в разделе **Параметры** щелкните **Подсети**, чтобы открыть страницу **Подсети**.

3. На странице **Подсети** щелкните **Подсеть шлюза**. Откроется страница **Добавление подсети**.

   ![Добавить подсеть шлюза](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Add the gateway subnet")

4. В поле **Имя** автоматически добавляется значение *GatewaySubnet*. По этому имени Azure идентифицирует подсеть как подсеть шлюза. Настройте автоматическое заполнение значений **диапазона адресов** для соответствия требованиям конфигурации, а затем щелкните **OK**, чтобы создать подсеть.

   ![Добавление подсети](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Adding the subnet")