---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516997"
---
В этом сценарии будут выполняться следующие задачи:

* Создание балансировщика нагрузки, принимающего сетевой трафик через порт 80 и отправляющего трафик с балансировкой нагрузки на виртуальные машины web1 и web2.
* Создание правил NAT для доступа к виртуальным машинам, управляемым балансировщиком нагрузки, через удаленный рабочий стол или SSH.
* Создание проб работоспособности

![Сценарий использования балансировщика нагрузки](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)