---
title: Настройка службы Microsoft Push-уведомлений в центрах уведомлений Azure | Документация Майкрософт
description: Сведения о настройке параметров Microsoft Push Notification Service для концентратора уведомлений Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240318"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Microsoft Push Notification Service (MPNS) для концентратора уведомлений на портале Azure
В этой статье показано, как Настройка параметров Microsoft Push Notification Service (MPNS) для концентратора уведомлений Azure с помощью портала Azure. 

## <a name="prerequisites"></a>Технические условия
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Настройка Microsoft Push Notification Service (MPNS)

В следующей процедуре представлены шаги по настройке параметров Microsoft Push Notification Service (MPNS) для концентратора уведомлений: 

1. На портале Azure на **концентратора уведомлений** выберите **Windows Phone (MPNS)** в меню слева.
1. Включите push-уведомления с проверкой подлинности или без нее:

   a. Чтобы включить push-уведомления без проверки подлинности, выберите **Включить push-уведомления без проверки подлинности** > **Сохранить**.

      ![Снимок экрана, на котором показано, как включить push-уведомления без проверки подлинности](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   2\. Чтобы включить push-уведомления с проверкой подлинности, сделайте следующее:
      * На панели инструментов щелкните **Отправка сертификата**.
      * Щелкните значок файла, а затем выберите файл сертификата.
      * Введите пароль для сертификата.
      * Нажмите кнопку **ОК**.
      * На странице **Windows Phone(MPNS)** выберите **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
Учебник с пошаговыми инструкциями по Push-уведомления на устройства Windows Phone с помощью центров уведомлений Azure и Microsoft Push Notification Service (MPNS), см. в разделе [Push-уведомления в приложения Windows Phone с помощью уведомлений Концентраторы](notification-hubs-windows-mobile-push-notifications-mpns.md).

