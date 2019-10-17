---
title: Добавление push-уведомлений в приложение Xamarin.iOS с помощью службы приложений Azure
description: Использование службы приложений Azure для отправки push-уведомлений в приложение Xamarin.iOS
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 5f968de25226528cb7aa016a4c301d07e53f2695
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388390"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Добавление push-уведомлений в приложение Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы для разработки мобильных приложений. Разработчики могут использовать службы **сборки**, **тестирования** и **распространения** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать состояние и использование своих приложений с помощью служб **аналитики** и **диагностики** , а также привлекать пользователей с помощью службы **push-уведомлений** . Разработчики также **могут использовать проверку подлинности** для аутентификации пользователей и службы **данных** , чтобы сохранять и синхронизировать данные приложений в облаке.
>
> Если вы хотите интегрировать облачные службы в мобильное приложение, зарегистрируйтесь в [центре приложений](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) уже сегодня.

## <a name="overview"></a>Краткое описание

В этом руководстве мы добавим push-уведомления в [проект быстрого запуска Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md), чтобы при каждом добавлении новой записи на устройство отправлялось push-уведомление.

Если вы не используете скачанный проект сервера, вам потребуется добавить пакет расширений для push-уведомлений. Дополнительные сведения о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Технические условия

* Ознакомьтесь с [кратким руководством по Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .
* Физическое устройство iOS. Push-уведомления не поддерживаются в симуляторе iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Зарегистрируйте приложение для push-уведомлений на портале разработчика Apple.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Настройка мобильного приложения для отправки push-уведомлений

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Обновление серверного проекта для отправки push-уведомлений

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Настройка проекта Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Добавьте в приложения возможность использования push-уведомлений

1. В **QSTodoService** добавьте приведенное ниже свойство, чтобы **AppDelegate** мог получить мобильный клиент.

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Добавьте следующий оператор `using` в верхнюю часть файла **AppDelegate.cs** .

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. В **AppDelegate** переопределите событие **FinishedLaunching**.

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. В этом же файле переопределите событие `RegisteredForRemoteNotifications`. В этом коде регистрируется простое шаблонное уведомление, которое будет рассылаться сервером по всем поддерживаемым платформам.

    Дополнительные сведения о шаблонах центров уведомлений см. в статье [Шаблоны](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Затем переопределите событие **DidReceivedRemoteNotification** :

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## <a name="test"></a>Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Выполнить**, чтобы выполнить сборку проекта и запустить приложение на устройстве iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

   > [!NOTE]
   > Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите задачу, а затем щелкните значок плюса ( **+** ).
3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК** , чтобы закрыть его.
4. Повторите шаг 2 и сразу закройте приложение, затем убедитесь, что уведомление отображается.

Вы успешно завершили ознакомление с данным учебником.
