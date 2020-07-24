---
author: baanders
description: включить файл для проверки назначения роли в программе установки Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e5e4bced12ffe9dc674b25a9a6513218cf8989e4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098103"
---
Один из способов проверить, успешно ли настроено назначение ролей, — просмотреть назначения ролей для экземпляра Digital двойников в портал Azure. На странице портала в [экземплярах Azure Digital двойников](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)выберите имя экземпляра, который требуется проверить. Затем просмотрите все назначенные роли в разделе *Управление доступом (IAM) > назначения ролей*. Пользователь должен отображаться в списке с ролью *владельца Azure Digital двойников (Предварительная версия)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/verify-role-assignment.png" alt-text="Просмотр назначений ролей для экземпляра цифрового двойников Azure в портал Azure":::