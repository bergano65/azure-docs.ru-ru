---
title: Настройка Google Firebase Cloud Messaging в центрах уведомлений Azure | Документация Майкрософт
description: Узнайте, как настроить концентратор уведомлений Azure с параметрами Google Firebase Cloud Messaging.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60239186"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Google Firebase Cloud Messaging (FCM) для концентратора уведомлений на портале Azure
В этой статье показано, как Настройка параметров Google Firebase Cloud Messaging (FCM) для концентратора уведомлений Azure с помощью портала Azure.  

## <a name="prerequisites"></a>Технические условия
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Настройка Google Firebase Cloud Messaging (FCM)

В следующей процедуре представлены шаги по настройке параметров Google Firebase Cloud Messaging (FCM) для концентратора уведомлений: 

1. На портале Azure на **концентратора уведомлений** выберите **Google (GCM или FCM)** в меню слева. 
2. В соответствующее поле вставьте **ключ API** для проекта FCM, сохраненного ранее. 
3. Щелкните **Сохранить**. 

   ![Снимок экрана, на котором показано, как настроить Центры уведомлений для Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Дальнейшие действия
Учебник с пошаговыми инструкциями по Push-уведомления на устройства Android с помощью центров уведомлений Azure и Google Firebase Cloud Messaging, см. в разделе [Push-уведомления на устройства Android с помощью центров уведомлений и Google FCM ](notification-hubs-android-push-notification-google-fcm-get-started.md).

