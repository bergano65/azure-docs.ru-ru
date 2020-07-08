---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 09/18/2019
ms.openlocfilehash: 805bf1d4dab9883c049d4d33c7d0e9633db315f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789411"
---
> [!IMPORTANT]
> Будьте внимательны при выборе триггера и действия, которые имеют одинаковый тип соединителя и используют их для работы с одной и той же сущностью, такой как очередь обмена сообщениями или подписка раздела. Это сочетание может создать бесконечный цикл, что приведет к созданию приложения логики, которое никогда не будет завершено.