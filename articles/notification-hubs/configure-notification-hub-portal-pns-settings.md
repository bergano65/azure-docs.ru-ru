---
title: Настройка концентратора уведомлений Azure с помощью параметров PNS | Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как настроить концентратор уведомлений на портале Azure с помощью параметров системы отправки уведомлений платформы (PNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314027"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>Настройка концентратора уведомлений Azure с помощью параметров системы отправки уведомлений платформы на портале Azure 
Центры уведомлений Azure обеспечивают простой в использовании и масштабируемый механизм отправки push-уведомлений, который позволяет отправлять уведомления на любую платформу (iOS, Android, Windows, Kindle, Baidu и т. д.) из любой серверной части (облачной или локальной). Дополнительные сведения о службе см. в статье [Что такое Центры уведомлений Azure?](notification-hubs-push-notification-overview.md).

[Создайте концентратор уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md), если вы этого еще не сделали. В этом кратком руководстве вы узнаете, как настроить концентратор уведомлений на портале Azure с помощью параметров системы отправки уведомлений платформы (PNS).

## <a name="apple-push-notification-service-apns"></a>Служба push-уведомлений Apple (APNS)
1. На странице **центра уведомлений** на портале Azure выберите **Apple (APNS)** в **Параметрах** в меню слева.
2. При выборе **сертификата** выполните следующие действия:
    1. Щелкните **значок файла** и выберите файл **.p12** для отправки. 
    2. Введите **пароль**.
    3. Выберите режим **Песочница**. Используйте **рабочую среду**, только если push-уведомления нужно отправлять пользователям, выполнившим покупку приложения в магазине.

        ![Настройка сертификации APNS на портале Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. При выборе **токена** выполните следующие действия: 
    1. Введите значения для **идентификатора ключа**, **пакета**, **идентификатора команды** и **токена**.
    2. Выберите режим **Песочница**. Используйте **рабочую среду**, только если push-уведомления нужно отправлять пользователям, выполнившим покупку приложения в магазине.

        ![Настройка токена APNS на портале Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Полное руководство по отправке push-уведомлений на устройствах iOS с помощью Центров уведомлений Azure и Службы push-уведомлений Apple (APNS) см. [здесь](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)
1. На странице **центра уведомлений** на портале Azure выберите **Google (GCM/FCM)** в **параметрах** в меню слева. 
2. Вставьте **ключ сервера** для проекта FCM, сохраненного ранее. 
3. На панели инструментов щелкните **Сохранить**. 

    ![Центры уведомлений Azure в Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Вы увидите сообщение в оповещениях о том, что центры уведомлений успешно обновлены. Кнопка **Сохранить** отключена. 

Полное руководство по отправке push-уведомлений на устройства Android с помощью Центров уведомлений Azure и Google Firebase Cloud Messaging см. [здесь](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service-wns"></a>Служба push-уведомлений Windows (WNS)
1. На странице **центра уведомлений** на портале Azure выберите **Windows (WNS)** в **Параметрах** в меню слева.
2. Введите значения для **идентификатора безопасности пакета** и **ключа безопасности**.
3. На панели инструментов щелкните **Сохранить**.

    ![Поля "Идентификатор безопасности пакета" и "Ключ безопасности"](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Полное руководство по отправке push-уведомлений в приложение универсальной платформы Windows (UWP), работающее на устройстве Windows, см. [здесь](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone — служба push-уведомлений Майкрософт
1. На странице **центра уведомлений** на портале Azure выберите **Windows Phone (MPNS)** в **параметрах**.
2. Если вы хотите включить push-уведомления без проверки подлинности, выберите **Включить push-уведомления без проверки подлинности**, а затем — **Сохранить** на панели инструментов.

    ![Портал Azure. Включение push-уведомлений без проверки подлинности](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. Если вы хотите использовать push-уведомления **без проверки подлинности**, выполните следующие действия:
    1. На панели инструментов щелкните **Загрузить сертификат**.
    2. Щелкните **значок файла** и выберите файл сертификата.
    3. Введите **пароль** для сертификата. 
    4. Выберите **ОК**, чтобы закрыть страницу **Загрузить сертификат**. 
    5. На странице **Windows Phone(MPNS)** выберите **Сохранить** на панели инструментов.

Полное руководство по отправке push-уведомлений на Windows Phone 8 с помощью службы push-уведомлений (Майкрософт) (MPNS) см. [здесь](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging-adm"></a>Amazon Device Messaging (ADM)
1. На странице **центра уведомлений** на портале Azure выберите **Amazon (ADM)** в **параметрах** в меню слева.
2. Введите значения для **идентификатора клиента** и **секрета клиента**.
3. На панели инструментов щелкните **Сохранить**.
    
    ![Центры уведомлений Azure — параметры ADM](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Полное руководство по использованию отправки push-уведомлений Центров уведомлений Azure в приложение Kindle см. [здесь](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)
1. На странице **центра уведомлений** на портале Azure выберите **Baidu (Android China)** в **параметрах** в меню слева. 
2. Введите **ключ API**, полученный из консоли Baidu, в проекте службы push-уведомлений облака Baidu. 
3. Введите **секретный ключ**, полученный из консоли Baidu, в проекте службы push-уведомлений облака Baidu. 
4. На панели инструментов щелкните **Сохранить**. 

    ![Центры уведомлений Microsoft Azure — Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. Вы увидите сообщение в оповещениях о том, что центры уведомлений успешно обновлены. Кнопка **Сохранить** отключена. 

Полное руководство по отправке push-уведомлений с помощью Центров уведомлений Azure и службы push-уведомлений облака Baidu см. [здесь](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Дополнительная информация
В этом кратком руководстве вы узнали, как настроить разные системы отправки уведомлений платформы для концентратора уведомлений на портале Azure. 

Полные пошаговые инструкции для отправки push-уведомлений для разных платформ см. в руководствах в разделе **Руководства**.

- [Руководство. Отправка push-уведомлений в приложения iOS с помощью Центров уведомлений Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md).
- [Руководство. Отправка push-уведомлений на устройства Android с помощью Центров уведомлений Azure и Google Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).
- [Руководство. Отправка уведомлений в приложения универсальной платформы Windows с использованием Центров уведомлений Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
- [Руководство. Отправка push-уведомлений в конкретные приложения Windows Phone с помощью Центров уведомлений Azure](notification-hubs-windows-mobile-push-notifications-mpns.md).
- [Приступая к работе с Центрами уведомлений для приложений Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).
- [Приступая к работе с Центрами уведомлений с помощью Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
