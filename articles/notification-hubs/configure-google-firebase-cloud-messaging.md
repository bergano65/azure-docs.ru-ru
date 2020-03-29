---
title: Нанастройка облачных сообщений Google Firebase в концентрах уведомлений Azure (ru) Документы Майкрософт
description: Узнайте, как настроить концентратор уведомлений Azure с настройками облачных сообщений Google Firebase.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127474"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройте настройки Google Firebase для концентратора уведомлений на портале Azure

В этой статье показано, как настроить настройки облачных сообщений Google Firebase Cloud Messaging (FCM) для концентратора уведомлений Azure с помощью портала Azure.  

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Наймите облачные сообщения Google Firebase (FCM)

Следующая процедура дает вам шаги для настройки Google Firebase Cloud Messaging (FCM) для концентратора уведомлений: 

1. На портале Azure на странице **концентратора уведомлений** выберите **Google (GCM/FCM)** в левом меню. 
2. В соответствующее поле вставьте **ключ API** для проекта FCM, сохраненного ранее. 
3. Нажмите кнопку **Сохранить**. 

   ![Снимок экрана, на котором показано, как настроить Центры уведомлений для Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Дальнейшие действия
Для руководства с пошаговыми инструкциями для нажатия уведомлений на устройства Android с помощью [Push notifications to Android devices by using Notification Hubs and Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)azure Notification Hubs и Google Firebase Cloud Messaging см.

