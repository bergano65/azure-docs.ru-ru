---
title: Настройка Cлужба push-уведомлений Apple в центрах уведомлений Azure | Документация Майкрософт
description: Узнайте, как настроить центр уведомлений Azure с помощью параметров Cлужба push-уведомлений Apple (APNS).
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
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Cлужба push-уведомлений Apple (APNS) для центра уведомлений в портал Azure
В этой статье показано, как настроить параметры Cлужба push-уведомлений Apple (APNS) для центра уведомлений Azure с помощью портал Azure. 

## <a name="prerequisites"></a>предварительным требованиям
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Настройка Cлужба push-уведомлений Apple

В следующей процедуре приведены действия по настройке параметров Cлужба push-уведомлений Apple (APNS) для центра уведомлений.

1. В портал Azure на странице **Центр уведомлений** выберите **Apple (APNs)** в меню слева.

1. В разделе **Режим проверки подлинности** выберите **Сертификат** или **Токен**.

   a. Если вы выбрали **Сертификат**:
   * Щелкните значок файла и выберите файл *.p12*, который нужно отправить.
   * Введите пароль.
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки сертификата APNS на портале Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Если вы выбрали **Токен**:

   * Введите значения для параметров **Идентификатор ключа**, **Идентификатор набора**, **Идентификатор команды** и **Токен**.
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки токена APNS на портале Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Дополнительная информация
Пошаговые инструкции по отправке уведомлений на устройства iOS см. в следующей статье: [Push-уведомления на устройствах iOS с помощью концентраторов уведомлений и APNs](notification-hubs-ios-apple-push-notification-apns-get-started.md) .
