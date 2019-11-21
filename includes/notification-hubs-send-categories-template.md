---
title: включение файла
description: включение файла
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 48907713082ebb1008ad963121671b36af7f2731
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228155"
---
В этом разделе вы будете отправлять экстренные новости в виде шаблонных уведомлений с тегами из консольного приложения .NET.

1. Создайте в Visual Studio новое консольное приложение Visual C#.
    1. В меню выберите **Файл** > **Создать** > **Проект**.
    1. В окне **Создание проекта** выберите в списке шаблонов **Консольное приложение (.NET Framework)** для C# и нажмите кнопку **Далее**.
    1. Введите имя приложения.
    1. Для параметра **Решение**, выберите **Добавить к решению** и **Создать**, чтобы создать проект.

1. Выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**, а затем в окне консоли выполните следующую команду:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Это действие добавляет ссылку на пакет SDK для Центров уведомлений Azure с помощью пакета [Microsoft.Azure.NotificationHubs].

1. Откройте файл *Program.cs* и добавьте следующий оператор `using`:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. В класс `Program` добавьте следующий метод или замените его, если он уже существует:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Этот код отправляет шаблонное уведомление по каждому из шести тегов в массиве строк. Использование тегов гарантирует, что устройства будут получать уведомления только зарегистрированных категорий.

1. В предыдущем коде замените заполнители `<hub name>` и `<connection string with full access>` именем центра уведомлений и строкой подключения для *DefaultFullSharedAccessSignature*, полученными ранее на панели мониторинга центра уведомлений.

1. Добавьте следующие строки в метод `Main()`:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Выполните сборку консольного приложения.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
