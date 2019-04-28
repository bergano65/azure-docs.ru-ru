---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407756"
---
1. На портале перейдите к виртуальной сети, для которой необходимо создать шлюз.
2. На странице виртуальной сети в разделе **Параметры** щелкните **Подсети**, чтобы открыть страницу "Подсети".
3. На странице **Подсети** щелкните **+Подсеть шлюза** в верхней части страницы. Откроется страница **Добавление подсети**.

   ![Добавить подсеть шлюза](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Add the gateway subnet")
  
4. В поле **Имя** автоматически добавляется значение GatewaySubnet. Значение GatewaySubnet необходимо для Azure при идентификации подсети в качестве подсети шлюза. Замените автоматически заполненное значение **диапазона адресов** в соответствии с требованиями к конфигурации.

   ![Добавление подсети шлюза](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Adding the gateway subnet")
  
5. Чтобы создать подсеть, в нижней части страницы щелкните **ОК**.
