---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67185790"
---
Компонент мобильных приложений в службе приложений Azure использует [Центры уведомлений Azure] для отправки push-уведомлений, поэтому вам нужно настроить центр уведомлений для мобильного приложения.

1. На [портале Azure] щелкните **Службы приложений**, а затем выберите серверную часть приложения. В разделе **Параметры** выберите **Push**.
2. Чтобы добавить в приложение ресурс концентратора уведомлений, нажмите кнопку **Подключить**. Вы можете создать центр или подключиться к существующему.

    ![Настройка концентратора](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Теперь центр уведомлений подключен к серверной части проекта вашего мобильного приложения. Позднее вы настроите этот концентратор уведомлений, чтобы подключиться к системе отправки уведомлений платформы (PNS), которая отправляет push-уведомления на устройства.

[Портал Azure]: https://portal.azure.com/
[Концентраторы уведомлений Azure]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
