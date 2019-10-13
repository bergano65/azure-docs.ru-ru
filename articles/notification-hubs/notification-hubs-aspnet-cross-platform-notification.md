---
title: Отправка кроссплатформенных уведомлений пользователям с помощью центров уведомлений Azure (ASP.NET)
description: Узнайте, как использовать шаблоны центров уведомлений для отправки в одном запросе независимых от платформы уведомлений, предназначенных для всех платформ.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 8f4de88ed79ee802866579448681cfe6cee3e654
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293428"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Отправка кроссплатформенных уведомлений пользователям с помощью центров уведомлений

Это руководство построено на предыдущем учебном курсе, которое [Отправка уведомлений конкретным пользователям с помощью концентраторов уведомлений Azure]. В этом учебнике описывается, как отправлять уведомления на все устройства, зарегистрированные для определенного пользователя, прошедшего проверку подлинности. Этот подход требует нескольких запросов на отправку уведомлений каждой поддерживаемой клиентской платформе. Концентраторы уведомлений Azure поддерживают шаблоны, которые позволяют указать, каким образом конкретное устройство должно получать уведомления. Этот метод упрощает отправку кроссплатформенных уведомлений.

В этой статье показано, как использовать шаблоны для отправки уведомлений, нацеленных на все платформы. В этой статье для отправки независимого от платформы уведомления используется один запрос. Более подробные сведения о шаблонах см. в разделе [Общие сведения о концентраторах уведомлений][Templates].

> [!IMPORTANT]
> Windows Phone проекты 8,1 и более ранних версий не поддерживаются в Visual Studio 2019. Дополнительные сведения см. в разделе [Целевая платформа и совместимость для Visual Studio 2019](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> С помощью концентраторов уведомлений устройство может зарегистрировать несколько шаблонов, используя один и тот же тег. В этом случае входящее сообщение, предназначенное для тега, приводит к поставке нескольких уведомлений на устройство, по одному для каждого шаблона. Этот процесс дает возможность отобразить одно сообщение в нескольких визуальных уведомлениях, например в виде значка и в виде всплывающего уведомления в приложении Магазина Windows.

## <a name="send-cross-platform-notifications-using-templates"></a>Отправка кроссплатформенных уведомлений с использованием шаблонов

В этом разделе используется пример кода, созданный в учебнике [Отправка уведомлений конкретным пользователям с помощью концентраторов уведомлений Azure] . Пример можно загрузить в репозитории [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Чтобы отправлять межплатформенные уведомления с помощью шаблонов, выполните следующие действия.

1. В **Обозреватель решений**Visual Studio разверните папку **Controllers** , а затем откройте файл *RegisterController.CS* .

1. Найдите в методе `Put` блок кода, который создает регистрацию, и замените содержимое `switch` следующим кодом.

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Этот код вызывает метод, используемый на конкретной платформе для регистрации шаблонов вместо собственной регистрации. Регистрация шаблонов является производной от собственной регистрации. Поэтому существующие регистрации изменять не нужно.

1. В **Обозреватель решений**в папке **Controllers** откройте файл *NotificationsController.CS* . Замените метод `Post` следующим кодом:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Этот код отправляет уведомление на все платформы в одно и то же время. Вы не указываете собственные полезные данные. Концентраторы уведомлений строят и доставляют правильные полезные данные на каждое устройство с указанным значением тега, как указано в зарегистрированных шаблонах.

1. Повторно опубликуйте проект веб-API.

1. Запустите клиентское приложение еще раз, чтобы убедиться, что регистрация выполнена успешно.

1. При необходимости разверните клиентское приложение на втором устройстве, а затем запустите приложение. Уведомление отображается на каждом из устройств.

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы завершили работу с этим руководством, Узнайте больше о концентраторах уведомлений и шаблонах в следующих статьях:

* Сведения о различных сценариях использования шаблонов см. в руководстве [Push-уведомления для конкретных устройств Windows, на которых выполняются универсальная платформа Windows приложения][Use Notification Hubs to send breaking news] .
* Более подробные сведения о шаблонах см. в разделе [Общие сведения о концентраторах уведомлений][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Отправка уведомлений конкретным пользователям с помощью концентраторов уведомлений Azure]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
