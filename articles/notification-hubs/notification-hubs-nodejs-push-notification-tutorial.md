---
title: Отправка push-уведомлений с помощью Центров уведомлений Azure и Node.js
description: Узнайте, как использовать концентраторы уведомлений для отправки push-уведомлений из приложения Node.js.
keywords: push-уведомление, push-уведомления, push-уведомления node.js, push-уведомления ios
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bdeba401e99ad16555b9f6ea00017fc525302983
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995012"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Отправка push-уведомлений с помощью Центров уведомлений Azure и Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Обзор

> [!IMPORTANT]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут с использованием [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

В этом руководстве показано, как отправлять push-уведомления с помощью службы "Центры уведомлений Azure" непосредственно из приложения [Node.js](https://nodejs.org).

Описанные сценарии включают отправку push-уведомлений в приложения на следующих платформах:

- Android
- iOS
- Универсальные приложения Windows
- Windows Phone

## <a name="notification-hubs"></a>Центры уведомлений

Центры уведомлений Azure — это простая в использовании масштабируемая многоплатформенная инфраструктура для отправки push-уведомлений на мобильные устройства. Подробные сведения об инфраструктуре служб приведены на странице [Центры уведомлений Azure](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

## <a name="create-a-nodejs-application"></a>Создание приложения Node.js

Первый шаг этого руководства представляет собой создание пустого приложения Node.js. Указания по созданию приложения Node.js см. в статьях [Создание и развертывание простого веб-приложения Node.js][nodejswebsite], [Построение и развертывание приложения Node.js в облачной службе Azure][Node.js Cloud Service] (с использованием Windows PowerShell) или [Создание и развертывание веб-приложения Node.js в Azure с использованием WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Настройка приложения для использования центров уведомлений

Для использования центров уведомлений Azure необходимо загрузить и использовать [пакет Azure](https://www.npmjs.com/package/azure)для Node.js, который включает встроенный набор вспомогательных библиотек, взаимодействующих со службами push-уведомлений REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Использование диспетчера пакета Node (NPM) для получения пакета

1. В интерфейсе командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Linux), перейдите к папке, в которой вы создали пустое приложение.
2. Выполните команду `npm install azure-sb` в командном окне.
3. Выполнив команду `ls` или `dir` вручную, вы можете убедиться, что папка `node_modules` создана.
4. В этой папке найдите пакет **azure** , который содержит библиотеки для доступа к центру уведомлений.

> [!NOTE]
> Дополнительные сведения об установке NPM доступны в официальном [блоге о NPM](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Импорт модуля
С помощью текстового редактора добавьте в начало файла `server.js` приложения следующее:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Настройка подключения к центру уведомлений Azure

Объект `NotificationHubService` позволяет работать с концентраторами уведомлений. Следующий код создает объект `NotificationHubService` для центра уведомлений `hubname`. Добавьте его в начало файла `server.js` после оператора импорта модуля Аzure.

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Получите значение `connectionstring` подключения на [портал Azure], выполнив следующие действия.

1. В области навигации слева щелкните **Обзор**.
2. Выберите **Центры уведомлений**, затем щелкните центр, который хотите использовать. Можно ссылаться на [учебнике Приступая к работе Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Если вам нужна помощь с созданием нового центра уведомлений.
3. Выберите элемент **Параметры**.
4. Щелкните **Политики доступа**. Вы увидите строки подключения как для общего, так и для полного доступа.

![Портал Azure — центры уведомлений](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Строку подключения можно также получить с помощью командлета **Get-AzureSbNamespace** в [Azure PowerShell](/powershell/azureps-cmdlets-docs) или команды **azure sb namespace show** в [интерфейсе командной строки Azure](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Общая архитектура

Объект `NotificationHubService` предоставляет следующие экземпляры объекта для отправки push-уведомлений определенным устройствам и приложениям.

- **Android** — используйте объект `GcmService`, доступный в `notificationHubService.gcm`;
- **iOS** — используйте объект `ApnsService`, доступный в `notificationHubService.apns`;
- **Windows Phone** — используйте объект `MpnsService`, доступный в `notificationHubService.mpns`;
- **универсальная платформа Windows** — используйте объект `WnsService`, доступный в `notificationHubService.wns`.

### <a name="how-to-send-push-notifications-to-android-applications"></a>Практическое руководство: Отправка push-уведомлений в приложения Android

Объект `GcmService` предоставляет метод `send`, который может использоваться для отправки push-уведомлений в приложения Android. Метод `send` принимает следующие параметры:

- **Tags** — идентификатор тега. Если тег отсутствует, уведомление отправляется всем клиентам.
- **Payload** — полезные данные JSON или строковые полезные данные сообщения.
- **Callback** — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе **Payload** (Полезные данные) документа [About GCM Connection Server](https://developer.android.com/google/gcm/server.html#payload) (О сервере подключений GCM).

В следующем коде для отправки push-уведомления всем зарегистрированным клиентам используется экземпляр `GcmService`, предоставляемый `NotificationHubService`.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Практическое руководство: Отправка push-уведомлений в приложения iOS

Как и в случае с описанными выше приложениями Android, объект `ApnsService` предоставляет метод `send`, который может использоваться для отправки push-уведомлений в приложения iOS. Метод `send` принимает следующие параметры:

- **Tags** — идентификатор тега. Если тег отсутствует, уведомление отправляется всем клиентам.
- **Payload** — полезные данные JSON или строковые полезные данные сообщения.
- **Callback** — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе **Notification Payload** (Полезные данные уведомления) документа [Local and Push Notification Programming Guide](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) (Руководство по программированию локальных и push-уведомлений).

В следующем коде используется экземпляр `ApnsService`, предоставляемый `NotificationHubService`, для отправки оповещений всем клиентам:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Практическое руководство: Отправка push-уведомлений в приложения Windows Phone

Объект `MpnsService` предоставляет метод `send`, который может использоваться для отправки push-уведомлений в приложения Windows Phone. Метод `send` принимает следующие параметры:

- **Tags** — идентификатор тега. Если тег отсутствует, уведомление отправляется всем клиентам.
- **Payload** — полезные данные XML сообщения.
- **TargetName** - `toast` — уведомлений во всплывающем окне. `token` для уведомлений на плитке.
- **NotificationClass** — приоритет уведомления. Допустимые значения см. в разделе **HTTP Header Elements** (Элементы заголовка HTTP) документа [Pushing Notifications from a Server (Windows Phone)](https://msdn.microsoft.com/library/hh221551.aspx) (Push-уведомления от сервера (Windows Phone)).
- **Options** — необязательные заголовки запроса.
- **Callback** — функция обратного вызова.

Перечень допустимых значений `TargetName`, `NotificationClass` и параметров заголовка см. на странице [XNA Game Studio 4.0 Refresh](https://msdn.microsoft.com/library/hh221551.aspx).

В следующем примере кода для отправки всплывающего push-уведомления используется экземпляр `MpnsService`, предоставляемый `NotificationHubService`.

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Практическое руководство: Отправка push-уведомлений в приложения универсальной платформы Windows (UWP)

Объект `WnsService` предоставляет метод `send`, который может использоваться для отправки push-уведомлений в приложения универсальной платформы Windows.  Метод `send` принимает следующие параметры:

- **Tags** — идентификатор тега. Если тег отсутствует, уведомление отправляется всем зарегистрированным клиентам.
- **Payload** — полезные данные XML сообщения.
- **Type** — тип уведомления.
- **Options** — необязательные заголовки запроса.
- **Callback** — функция обратного вызова.

Список допустимых типов и заголовков запроса см. в разделе [Заголовки запроса и ответа службы push-уведомлений (приложения среды выполнения Windows)](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

В следующем коде для отправки всплывающего push-уведомления в приложение UWP используется экземпляр `WnsService`, предоставляемый `NotificationHubService`.

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Следующие шаги

Примеры фрагментов выше позволяют легко создать инфраструктуру службы для отправки push-уведомлений на широкий спектр устройств. Теперь, когда вы познакомились с основами использования центров уведомлений с Node.js, используйте следующие ссылки для получения дополнительных сведений о том, как можно дальше расширить эти возможности.

- См. статью [Общие сведения о Центрах уведомлений](https://msdn.microsoft.com/library/azure/jj927170.aspx) в справочнике MSDN.
- Дополнительные примеры и сведения о реализации доступны в репозитории [пакетов SDK Azure для Node] на сайте GitHub.

[пакетов SDK Azure для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[портал Azure]: https://portal.azure.com
