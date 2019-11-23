---
title: Configure Apple Push Notification Service in Azure Notification Hubs | Microsoft Docs
description: Learn how to configure an Azure notification hub with Apple Push Notification Service (APNS) settings.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 116af5192236045ec50409f65a9687ffec5223b5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406096"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure Apple Push Notification Service (APNS) settings for a notification hub in the Azure portal
This article shows you how to configure Apple Push Notification Service (APNS) settings for an Azure notification hub by using the Azure portal. 

## <a name="prerequisites"></a>Технические условия
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configure Apple Push Notification Service

The following procedure gives you steps to configure Apple Push Notification Service (APNS) settings for a notification hub:

1. In the Azure portal, on the **Notification Hub** page, select **Apple (APNS)** on the left menu.

1. В разделе **Режим проверки подлинности** выберите **Сертификат** или **Токен**.

   а) Если вы выбрали **Сертификат**:
   * Щелкните значок файла и выберите файл *.p12*, который нужно отправить.
   * Введите пароль.
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки сертификата APNS на портале Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   б) Если вы выбрали **Токен**:

   * Введите значения для параметров **Идентификатор ключа**, **Идентификатор набора**, **Идентификатор команды** и **Токен**.
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки токена APNS на портале Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Дальнейшие действия
For a tutorial with step-by-step instructions for pushing notifications to iOS devices, see the following article: [Push notifications to iOS devices by using Notification Hubs and APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
