---
title: Обновления в центрах уведомлений Azure для iOS 13 | Документация Майкрософт
description: Дополнительные сведения о критических изменениях iOS 13 в центрах уведомлений Azure
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228142"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Обновление службы "Центры уведомлений Azure" для iOS 13

Недавно корпорация Apple внесла некоторые изменения в общедоступную службу push-уведомлений. изменения в основном согласованы с выпусками iOS 13 и Xcode. В этой статье описывается влияние этих изменений в центрах уведомлений Azure.

## <a name="apns-push-payload-changes"></a>Изменения полезных данных отправки APNS

### <a name="apns-push-type"></a>Тип отправки APNS

Apple теперь требует, чтобы разработчики определяли уведомления в виде оповещений или фоновых уведомлений через новый заголовок `apns-push-type` в API APNS. В соответствии с [документацией Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "значение этого заголовка должно точно отражать содержимое полезных данных уведомления. Если имеется несоответствие или в необходимых системах отсутствует заголовок, APNs может вернуть ошибку, задержать доставку уведомления или полностью отклонить его".

Теперь разработчики должны задать этот заголовок в приложениях, отправляющих уведомления через центры уведомлений Azure. Из-за технических ограничений клиенты должны использовать проверку подлинности на основе маркеров для учетных данных APNS с запросами, включающими этот атрибут. Если для учетных данных APNS используется проверка подлинности на основе сертификатов, необходимо переключиться на использование проверки подлинности на основе маркеров.

В следующих примерах кода показано, как задать атрибут заголовка в запросах на уведомления, отправляемых через центры уведомлений Azure.

#### <a name="template-notifications---net-sdk"></a>Уведомления шаблонов — пакет SDK для .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Собственные уведомления — пакет SDK для .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Прямые вызовы RESTFUL

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Чтобы помочь вам во время этого перехода, когда центры уведомлений Azure обнаруживают уведомление без установленного `apns-push-type`, служба выводит тип отправки из запроса уведомления и устанавливает значение автоматически. Помните, что необходимо настроить центры уведомлений Azure на использование проверки подлинности на основе маркеров для задания требуемого заголовка; Дополнительные сведения см. в статье [Проверка подлинности на основе маркеров (http/2) для APNs](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Приоритет APNS

Еще одно незначительное изменение, которое требует изменения серверного приложения, отправляющего уведомления, является требованием для фоновых уведомлений. Теперь заголовок `apns-priority` должен иметь значение 5. Во многих приложениях для заголовка `apns-priority` задается значение 10 (указывает немедленное доставкой) или не задаются и не устанавливаются значения по умолчанию (а также 10).

Установка этого значения равным 10 больше не разрешена для фоновых уведомлений, и необходимо задать значение для каждого запроса. Apple не будет предоставлять фоновые уведомления, если это значение отсутствует. Например,

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Изменения пакета SDK

В течение года разработчики iOS использовали атрибут `description` `deviceToken` данных, отправленных в делегата Push-маркера, для извлечения токена push-уведомлений, используемого внутренним приложением для отправки уведомления на устройство. В Xcode 11 это `description` атрибут изменился на другой формат. Существующий код, используемый разработчиками для этого атрибута, теперь нарушен. Мы обновили пакет SDK для центров уведомлений Azure в соответствии с этим изменением, поэтому обновите пакет SDK, используемый вашими приложениями, до версии 2.0.4 или более новой версии [пакета Azure Notification](https://github.com/Azure/azure-notificationhubs-ios)Hub для iOS.
