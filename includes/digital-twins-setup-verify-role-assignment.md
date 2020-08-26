---
author: baanders
description: включить файл для проверки назначения роли в программе установки Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864747"
---
Один из способов проверить, успешно ли настроено назначение ролей, — просмотреть назначения ролей для экземпляра Digital двойников в [портал Azure](https://portal.azure.com). Перейдите к своему экземпляру Azure Digital двойников в портал Azure (его можно просмотреть на странице [экземпляров Azure Digital двойников](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) или выполнить поиск по имени на панели поиска портала).

Затем просмотрите все назначенные роли в разделе *Управление доступом (IAM) > назначения ролей*. Пользователь должен отображаться в списке с ролью *владельца Azure Digital двойников (Предварительная версия)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Просмотр назначений ролей для экземпляра цифрового двойников Azure в портал Azure":::