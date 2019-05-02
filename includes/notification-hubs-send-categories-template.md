---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f0ff729084d194ff2e05e89eadc45782f775b1c5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64929443"
---
В этом разделе вы будете отправлять экстренные новости в виде шаблонных уведомлений с тегами из консольного приложения .NET. 

1. В Visual Studio создайте консольное приложение Visual C#: a. В меню выберите **Файл** > **Создать** > **Проект**.
    b. Разверните раздел **Visual C#** и выберите **Классическое приложение Windows**. 
    c. Выберите **Консольное приложение (.NET Framework)** в списке шаблонов. 
    d. Введите **имя** приложения. 
    д. Выберите **папку** для приложения.
    Е. Нажмите кнопку **ОК**, чтобы создать проект. 
2. В главном меню Visual Studio выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**, а затем в окне консоли введите следующую строку:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Нажмите клавишу **ВВОД**.  
    Это действие добавляет ссылку на пакет SDK для Центров уведомлений Azure с помощью [пакета NuGet Microsoft.Azure.Notification Hubs].

4. Откройте файл Program.cs и добавьте следующий оператор `using`:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. В класс `Program` добавьте следующий метод или замените его, если он уже существует:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Этот код отправляет шаблонное уведомление по каждому из шести тегов в массиве строк. Использование тегов гарантирует, что устройства будут получать уведомления только зарегистрированных категорий.

5. В предыдущем коде замените заполнители `<hub name>` и `<connection string with full access>` именем центра уведомлений и строкой подключения для *DefaultFullSharedAccessSignature*, полученными ранее на панели мониторинга центра уведомлений.

6. Добавьте следующие строки в метод **Main**:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Выполните сборку консольного приложения.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[пакета NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/ (Центры уведомлений Microsoft Azure 1.0.7)
