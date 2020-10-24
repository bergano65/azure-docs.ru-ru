---
author: baanders
description: включить файл для проверки назначения роли в программе установки Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489050"
---
Один из способов проверить, успешно ли настроено назначение ролей, — просмотреть назначения ролей для экземпляра Digital двойников в [портал Azure](https://portal.azure.com). Перейдите к своему экземпляру Azure Digital двойников в портал Azure (его можно просмотреть на странице [экземпляров Azure Digital двойников](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) или выполнить поиск по имени на панели поиска портала).

Затем просмотрите все назначенные роли в разделе *Управление доступом (IAM) > назначения ролей*. Пользователь должен отображаться в списке с ролью *владельца данных Azure Digital двойников*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Просмотр назначений ролей для экземпляра цифрового двойников Azure в портал Azure":::