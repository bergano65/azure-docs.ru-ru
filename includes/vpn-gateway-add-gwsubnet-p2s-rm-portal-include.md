---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc1e2803a420b95e1abec0886733c5e42a8cfdb4
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026648"
---
1. На [портале](http://portal.azure.com) перейдите к виртуальной сети Resource Manager, для которой необходимо создать шлюз.
2. На странице виртуальной сети в разделе **Параметры** щелкните **Подсети**, чтобы открыть страницу **Подсети**.
3. На странице **Подсети** щелкните **+Подсеть шлюза**. Откроется страница **Добавление подсети**. 

  ![Добавить подсеть шлюза](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Add the gateway subnet")
4. В поле **Имя** автоматически добавляется значение GatewaySubnet. По этому имени Azure идентифицирует подсеть как подсеть шлюза. Замените автоматически заполненное значение **диапазона адресов** в соответствии с требованиями к конфигурации. Не настраивайте таблицу маршрутов или конечные точки службы.

  ![Добавление подсети](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Adding the subnet")
5.  Чтобы создать подсеть, в нижней части страницы нажмите кнопку **ОК**.