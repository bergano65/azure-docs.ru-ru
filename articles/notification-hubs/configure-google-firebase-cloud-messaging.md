---
title: Настройка облачных сообщений Google Firebase в центрах уведомлений Azure | Документация Майкрософт
description: Узнайте, как настроить центр уведомлений Azure с помощью Google Firebase Cloud Messaging Settings.
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
ms.openlocfilehash: 803289476d4d8869f7828b2843a983ee0c86bfbd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212493"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Google Firebase Cloud Messaging (FCM) для центра уведомлений в портал Azure
В этой статье показано, как настроить параметры Google Firebase Cloud Messaging (FCM) для центра уведомлений Azure с помощью портал Azure.  

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Настройка Google Firebase Cloud Messaging (FCM)

Следующая процедура позволяет настроить параметры Google Firebase Cloud Messaging (FCM) для центра уведомлений. 

1. В портал Azure на странице **Центр уведомлений** выберите **Google (gcm/FCM)** в меню слева. 
2. В соответствующее поле вставьте **ключ API** для проекта FCM, сохраненного ранее. 
3. Щелкните **Сохранить**. 

   ![Снимок экрана, на котором показано, как настроить Центры уведомлений для Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Следующие шаги
Пошаговые инструкции по отправке уведомлений на устройства Android с помощью центров уведомлений Azure и Google Firebase Cloud Messaging см. в статье [Push-уведомления для устройств Android с помощью концентраторов уведомлений и Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

