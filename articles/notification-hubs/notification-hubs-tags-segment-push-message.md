---
title: Выражения реуктора и тегов в концентрах уведомлений Azure
description: Узнайте, как направлять и отмечать выражения для концентраторов уведомлений Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062313"
---
# <a name="routing-and-tag-expressions"></a>Маршрутизация и выражения тегов

## <a name="overview"></a>Обзор

Выражения тегов позволяют выбирать определенные целевые наборы устройств, а точнее регистраций, при отправке push-уведомлений через центры уведомлений.

## <a name="targeting-specific-registrations"></a>Выбор регистраций

Единственный способ выбрать определенные регистрации для получения уведомлений — это связать с ними теги, а затем указывать эти теги в качестве назначения. Как говорится в [управлении регистрацией,](notification-hubs-push-notification-registration-management.md)для получения push-уведомлений приложение должно зарегистрировать ручку устройства на концентраторе уведомлений. Как только приложение создает регистрацию на концентраторе уведомлений, бэкэнд приложения может отправлять ему push-уведомления. Серверная часть приложения выбирает целевые регистрации для отправки определенных уведомлений следующим образом.

1. **Широковещательная рассылка**: уведомление получают все регистрации в центре уведомлений.
2. **По тегу**: уведомление получают все регистрации, которые содержат указанный тег.
3. **По выражению тега**: уведомление получают все регистрации, набор тегов которых соответствует указанному выражению.

## <a name="tags"></a>Теги

Тег может быть любой строкой, до 120 символов, содержащих алфавитные и следующие не-буквенно-цифровые символы:`-`',`_``@`',`#`',`.`',',`:`', ','. '. В следующем примере показано приложение, из которого можно получать всплывающие уведомления об определенных музыкальных группах. В этом случае простой способ маршрутизании уведомлений заключается в маркировке регистраций тегами, представляющими различные диапазоны, как на следующей цифре:

![Обзор тегов](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

На рисунке, сообщение помечены **с Beatles** достигает только таблетки, которые зарегистрированы с тегом **Beatles**.

Дополнительные сведения о создании регистраций по тегам см. в статье [Управление регистрацией](notification-hubs-push-notification-registration-management.md).

Можно отправлять уведомления для тегов, используя методы отправки уведомлений класса `Microsoft.Azure.NotificationHubs.NotificationHubClient` из пакета SDK [центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . Также можно использовать Node.js или интерфейсы API REST push-уведомлений.  Ниже приведен пример с использованием пакета SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Теги не должны быть предварительно подготовлены и могут относиться к нескольким концепциям, к онболеет приложения. Например, пользователи приложения из этого примера могут оставлять свои комментарии по группам и хотят получать всплывающие уведомления не только о комментариях по своим любимым группам, но и обо всех комментариях от своих друзей, независимо от того, какую группу они комментируют. На примере такого сценария приводится следующий рисунок:

![Теги друзей](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

В этом примере Алиса интересуется обновлениями для The Beatles, а Боб — обновлениями для Wailers. Боб также заинтересован в комментариях Чарли, и Чарли заинтересован в Wailers. Когда уведомление отправляется для комментария Чарли на Beatles, Уведомления концентраторы отправляет его как Алиса и Боб.

В то время как вы можете кодировать `band_Beatles` `follows_Charlie`несколько проблем в тегах (например, или), теги являются простыми строками, а не свойствами с значениями. Регистрация совпадает только при наличии или отсутствии определенного тега.

Полное пошаговое руководство по использованию тегов для отправки сообщений по группам интересов см. в статье [Использование Центров уведомлений для передачи экстренных новостей](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Центры уведомлений Azure поддерживают не более 60 тегов для каждой регистрации.

## <a name="using-tags-to-target-users"></a>Использование тегов для выбора конечных пользователей

Другой способ использования тегов — идентифицировать все устройства, связанные с конкретным пользователем. Вы можете пометить Регистрацию с тегом, который содержит идентификатор пользователя, как и на следующем рисунке:

![Пользователи тегов](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

На рисунке помеченное `user_Alice` сообщение достигает всех `user_Alice`устройств, помеченных .

## <a name="tag-expressions"></a>Выражения тегов

Есть случаи, когда уведомления должны быть нацелены на набор регистраций, идентифицированных не одним тегом, а выражением Boolean с помощью тегов.

Рассмотрим спортивное приложение, которое отправляет напоминание всем пользователям в Бостоне об игре между командами Red Sox и Cardinals. Если в клиентском приложении регистрируются теги об интересе к командам и месту, то уведомление должно отправляться всем пользователям в Бостоне, интересующимся Red Sox или Cardinals. Это условие можно задать с помощью следующего логического выражения:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Выражения тегов](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Выражения тегов поддерживают общие `AND` операторы Boolean, такие как (`&&` `OR` , и`||` `NOT` ( )`!` они также могут содержать скобки. Выражения тегов, использующие только `OR` операторы, могут ссылаться на 20 тегов; выражение `AND` с операторами, но ни один оператор не `OR` может ссылаться на 10 тегов; в противном случае выражения тегов ограничены 6 тегами.

Вот пример для отправки уведомлений с выражениями тегов с помощью SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
