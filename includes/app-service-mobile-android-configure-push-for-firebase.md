---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185792"
---
1. На [портале Azure](https://portal.azure.com/) щелкните **Просмотреть все** > **Службы приложений**, а затем выберите серверную часть своего мобильного приложения. В разделе **Параметры** щелкните **App Service Push** (Push-уведомления службы приложений) и выберите имя центра уведомлений.
2. Выберите **Google (GCM)** , введите значение **ключа сервера**, полученное от Firebase в ходе предыдущей процедуры, и нажмите кнопку **Сохранить**.

    ![Указание ключа API на портале](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Теперь серверная часть вашего мобильного приложения настроена для использования Firebase Cloud Messaging. Это позволяет отправлять push-уведомления приложению Android через центр уведомлений.
