---
author: baanders
description: включить файл для проверки назначения роли в программе установки Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405601"
---
Один из способов проверить, успешно ли настроено назначение ролей, — просмотреть назначения ролей для экземпляра Digital двойников в [портал Azure](https://portal.azure.com). Перейдите на страницу портала [экземпляров Azure Digital двойников](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (вы можете использовать эту ссылку или найдите ее на панели поиска портала) и выберите имя экземпляра, который необходимо проверить. 

Затем просмотрите все назначенные роли в разделе *Управление доступом (IAM) > назначения ролей*. Пользователь должен отображаться в списке с ролью *владельца Azure Digital двойников (Предварительная версия)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Просмотр назначений ролей для экземпляра цифрового двойников Azure в портал Azure":::