---
title: Как отправлять запланированные уведомления | Документация Майкрософт
description: В этом разделе описывается использование запланированных уведомлений с помощью центров уведомлений Azure.
services: notification-hubs
documentationcenter: .net
keywords: push-уведомления, push-уведомление, планирование push-уведомлений
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 56eedda7f79fedce1e34ad837c92006e5cd8f191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998277"
---
# <a name="how-to-send-scheduled-notifications"></a>Практическое руководство. Отправка запланированных уведомлений

Предположим, возникла необходимость отправить уведомление в какой-либо момент в будущем, но у вас нет простого способа пробудить внутренней код для отправки уведомления. Центры уведомлений уровня "Стандартный" поддерживают функцию, которая позволяет запланировать уведомления на будущее до 7 дней.


## <a name="schedule-your-notifications"></a>Планирование уведомлений
При отправке уведомления просто используйте [ `ScheduledNotification` класс](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification?view=azure-dotnet#microsoft_azure_notificationhubs_schedulednotification) в пакете SDK для центров уведомлений, как показано в следующем примере:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Отмена запланированных уведомлений
Кроме того, вы можете отменить ранее запланированное уведомление, используя его notificationId.

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Количество запланированных уведомлений, которые можно отправлять, не ограничено.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими руководствами:

 - [Руководство. Отправка уведомлений в приложения универсальной платформы Windows с использованием Центров уведомлений Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Отправка локализованных push-уведомлений](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Отправка push-уведомлений определенным пользователям](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Отправка push-уведомлений с определением геозон с помощью Центров уведомлений Azure и Bing Spatial Data](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
