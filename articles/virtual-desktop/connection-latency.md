---
title: Задержка подключения пользователей виртуальных рабочих столов Windows — Azure
description: Задержка подключения для пользователей виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60779fb045aa612a6ba0988c4635752f973f60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82607407"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Определение задержки подключения пользователей в виртуальном рабочем столе Windows

Виртуальный рабочий стол Windows является глобально доступным. Администраторы могут создавать виртуальные машины в любом требуемом регионе Azure. Задержка подключения будет зависеть от расположения пользователей и виртуальных машин. Службы виртуальных рабочих столов Windows будут непрерывно выдвигаться на новые географические регионы для повышения задержки. 
 
[Средство оценки возможностей виртуальных рабочих столов Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) помогает определить лучшее расположение для оптимизации задержки виртуальных машин. Мы рекомендуем использовать средство каждые два – три месяца, чтобы убедиться, что оптимальное расположение не изменилось, так как виртуальный рабочий стол Windows разворачивается в новые области. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Виртуальный рабочий стол Windows использует диспетчер трафика Azure, который проверяет расположение DNS-сервера пользователя, чтобы найти ближайший экземпляр службы виртуальных рабочих столов Windows. Перед выбором расположения для виртуальных машин мы рекомендуем администраторам проверить расположение DNS-сервера пользователя.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы проверить лучшее расположение для оптимальной задержки, см. раздел [средство оценки возможностей виртуального рабочего стола Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Ценовые планы см. на странице [цены на виртуальные рабочие столы Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Чтобы приступить к работе с развертыванием виртуальных рабочих столов Windows, ознакомьтесь с [нашим руководством](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).