---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122163"
---
В этом сценарии будут выполняться следующие задачи:

* Создание балансировщика нагрузки, принимающего сетевой трафик через порт 80 и отправляющего трафик с балансировкой нагрузки на виртуальные машины web1 и web2.
* Создание правил NAT для доступа к виртуальным машинам, управляемым балансировщиком нагрузки, через удаленный рабочий стол или SSH.
* Создание проб работоспособности

![Сценарий использования балансировщика нагрузки](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
