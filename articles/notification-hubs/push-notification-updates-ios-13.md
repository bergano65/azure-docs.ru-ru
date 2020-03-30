---
title: Обновления извозобий Azure Концентраторы iOS 13 Документы Майкрософт
description: Узнайте о нарушениях iOS 13 в концентрах уведомлений Azure
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228142"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Обновления концентраторов уведомлений Azure для iOS 13

Apple недавно внесла некоторые изменения в свои общественные push-службы; изменения в основном совпадают с версиями iOS 13 и Xcode. В этой статье описывается влияние этих изменений на концентраторы уведомлений Azure.

## <a name="apns-push-payload-changes"></a>APNS нажимают изменения полезной нагрузки

### <a name="apns-push-type"></a>Тип нажатия APNS

Теперь Apple требует, чтобы разработчики идентифицировали уведомления `apns-push-type` как оповещение или фоновые уведомления через новый заголовок в APNS API. Согласно [документации Apple:](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)"Значение этого заголовка должно точно отражать содержимое полезной нагрузки вашего уведомления. Если есть несоответствие, или если заголовок отсутствует на требуемых системах, APNs может вернуть ошибку, отсрочить доставку уведомления, или отказаться от него вообще ".

Теперь разработчики должны настроить эту заголовок в приложениях, которые отправляют уведомления через концентраторы уведомлений Azure. Из-за технических ограничений клиенты должны использовать проверку подлинности на основе токенов для учетных данных APNS с запросами, которые включают этот атрибут. Если вы используете проверку подлинности на основе сертификатов для учетных данных APNS, необходимо переключиться на использование аутентификации на основе токенов.

Следующие примеры кода показывают, как установить этот атрибут заголовка в запросах уведомлений, отправляемых через концентраторы уведомлений Azure.

#### <a name="template-notifications---net-sdk"></a>Уведомления о шаблонах - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Родные уведомления - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Прямые звонки REST

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Чтобы помочь вам в этом переходе, когда концентраторы уведомлений Azure обнаруживают уведомление, у которого нет `apns-push-type` набора, служба делает вывод тип push из запроса уведомлений и автоматически устанавливает значение. Помните, что необходимо настроить концентраторы уведомлений Azure для использования проверки подлинности на основе токенов для установки необходимого заголовка; для получения дополнительной [Token-based (HTTP/2) Authentication for APNS](notification-hubs-push-notification-http2-token-authentification.md)информации см.

## <a name="apns-priority"></a>Приоритет APNS

Другим незначительным изменением, но требующим изменения в приложении бэкэнда, которое отправляет `apns-priority` уведомления, является требование о том, что для фоновых уведомлений заголовок теперь должен быть установлен на 5. Многие приложения `apns-priority` устанавливают заголовок до 10 (указывая на немедленную доставку), или не устанавливают его и получают значение по умолчанию (которое также составляет 10).

Установка этого значения до 10 больше не допускается для фоновых уведомлений, и необходимо установить значение для каждого запроса. Apple не будет предоставлять фоновые уведомления, если это значение отсутствует. Пример:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Изменения SDK

В течение многих лет `description` разработчики `deviceToken` iOS использовали атрибут данных, отправленных делегату токенов push, чтобы извлечь токен push, который приложение бэкэнда использует для отправки уведомлений на устройство. С Xcode 11 `description` этот атрибут был изменен на другой формат. Существующий код, используемый разработчиками для этого атрибута, теперь сломан. Мы обновили SDK-уведомления Azure Notification Hubs для этого изменения, поэтому, пожалуйста, обновите SDK, используемый вашими приложениями, до версии 2.0.4 или новой [версии SDK iOS-уведомления Azure.](https://github.com/Azure/azure-notificationhubs-ios)
