---
title: Настройте службу уведомлений Apple Push в концентрах уведомлений Azure (ru) Документы Майкрософт
description: Узнайте, как настроить концентратор уведомлений Azure с настройками службы уведомлений Apple Push (APNS).
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
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127517"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройте настройки службы уведомлений Apple Push для концентратора уведомлений на портале Azure

В этой статье показано, как настроить настройки службы уведомлений Apple Push (APNS) для концентратора уведомлений Azure с помощью портала Azure. 

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Настройка службы уведомлений Apple Push

Следующая процедура дает вам шаги для настройки настройки службы уведомлений Apple Push (APNS) для концентратора уведомлений:

1. На портале Azure на странице **концентратора уведомлений** выберите **Apple (APNS)** в левом меню.

1. В разделе **Режим проверки подлинности** выберите **Сертификат** или **Токен**.

   а. Если вы выбрали **Сертификат**:
   * Щелкните значок файла и выберите файл *.p12*, который нужно отправить.
   * Введите пароль.
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки сертификата APNS на портале Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Если вы выбрали **Токен**:

   * Введите значения для **Key ID,** **Комплект ID,** **Идентификатор команды**и **токен.**
   * Выберите режим **Песочница**. Если нужно отправлять push-уведомления пользователям, которые приобрели приложение в магазине, выберите режим **Рабочий**.

     ![Снимок экрана настройки токена APNS на портале Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Дальнейшие действия
Для руководства с пошаговыми инструкциями для нажатия уведомлений на устройства iOS смотрите следующую статью: [Push-уведомления на устройствах iOS с помощью концентраторов уведомлений и APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
