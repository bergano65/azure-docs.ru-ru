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
ms.openlocfilehash: 74e7e3c74934e292b668b8bff594a5efbca19716
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212544"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Cлужба push-уведомлений Apple (APNS) для центра уведомлений в портал Azure
В этой статье показано, как настроить параметры Cлужба push-уведомлений Apple (APNS) для центра уведомлений Azure с помощью портал Azure. 

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Настройка Cлужба push-уведомлений Apple

В следующей процедуре приведены действия по настройке параметров Cлужба push-уведомлений Apple (APNS) для центра уведомлений.

1. В портал Azure на странице **Центр уведомлений** выберите **Apple (APNs)** в меню слева.

1. В разделе **Режим проверки подлинности** выберите **Сертификат** или **Токен**.

   1\. Если вы выбрали **Сертификат**:
   * Щелкните значок файла и выберите файл *.p12*, который нужно отправить.
   * Введите пароль.
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки сертификата APNS на портале Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   2\. Если вы выбрали **Токен**:

   * Введите значения для параметров **Идентификатор ключа**, **Идентификатор набора**, **Идентификатор команды** и **Токен**.
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки токена APNS на портале Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Следующие шаги
Пошаговые инструкции по принудительной отправке уведомлений на устройства iOS см. в следующей статье: [Руководство. Отправка push-уведомлений в приложения iOS с помощью Центров уведомлений Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md)
