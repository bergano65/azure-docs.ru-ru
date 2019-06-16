---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140461"
---
1. На [портале Azure](https://portal.azure.com/) последовательно выберите **Browse All (Просмотреть все)**  > **Службы приложений**. Выберите серверную часть для функции "Мобильные приложения". В разделе **Параметры** выберите **Push-уведомления службы приложений**. Затем выберите имя концентратора уведомлений.
2. Откройте **Windows (WNS)** . Ведите **ключ безопасности** (секрет клиента) и **ИД безопасности пакета**, полученные на сайте служб Live. Щелкните **Сохранить**.

    ![Указание ключа WNS на портале](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Вы настроили серверную часть на использование WNS для отправки push-уведомлений.
