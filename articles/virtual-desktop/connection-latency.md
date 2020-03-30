---
title: Windows Virtual Desktop пользовательские задержки подключения - Azure
description: Задержка подключения для пользователей виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128190"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Определить задержку подключения пользователей в Windows Virtual Desktop

Windows Virtual Desktop доступен во всем мире. Администраторы могут создавать виртуальные машины (Виртуальные технологии) в любом заговорном регионе Azure. Задержка подключения будет варьироваться в зависимости от местоположения пользователей и виртуальных машин. Службы Windows Virtual Desktop будут постоянно внедряться в новые географические регионы для улучшения задержки. 
 
Инструмент [оценки опыта windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) может помочь вам определить наилучшее местоположение для оптимизации задержки ваших виртуальных технологий. Мы рекомендуем использовать инструмент каждые два-три месяца, чтобы убедиться, что оптимальное местоположение не изменилось по мере того, как Windows Virtual Desktop выкатывается в новые области. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Windows Virtual Desktop использует Azure Traffic Manager, который проверяет местоположение DNS-сервера пользователя, чтобы найти ближайший экземпляр службы Windows Virtual Desktop. Мы рекомендуем администраторам просмотреть местоположение DNS-сервера пользователя, прежде чем выбрать местоположение для VMs.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы проверить лучшее место для оптимальной задержки, см. [Windows Virtual Desktop Experience Estimator tool](https://azure.microsoft.com/services/virtual-desktop/assessment/)
- Для ценообразования планы, см [Windows Виртуальный настольный цены](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Чтобы начать работу с развертыванием Windows Virtual Desktop, ознакомьтесь с [нашим учебником.](tenant-setup-azure-active-directory.md)