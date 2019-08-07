---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780186"
---
Подсеть шлюза содержит зарезервированные IP-адреса, используемые службами шлюза виртуальной сети. Создайте подсеть шлюза.

1. На портале перейдите к виртуальной сети, для которой необходимо создать шлюз.
2. На странице виртуальной сети щелкните **Подсети**, чтобы развернуть страницу **VNet1 — подсети**.
3. Щелкните **+ Подсеть шлюза** в верхней части страницы. Откроется страница **Добавление подсети**.

   ![Добавить подсеть шлюза](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Add the gateway subnet")
4. В обязательное поле **Имя** автоматически добавляется значение GatewaySubnet. Измените автоматически заполненный **диапазон адресов (блок CIDR)** , чтобы он соответствовал следующему значению:

   **Диапазон адресов (блок CIDR)** . 10.1.255.0/27

   ![Добавить подсеть шлюза](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Add the gateway subnet")
5. Оставьте остальные параметры по умолчанию: **нет** или **0**. Затем нажмите кнопку **ОК** , чтобы создать подсеть шлюза.