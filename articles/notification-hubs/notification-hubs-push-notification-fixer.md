---
title: Диагностика пропущенных уведомлений в Центрах уведомлений Azure
description: Узнайте, как определить распространенные проблемы с пропущенными уведомлениями в Центрах уведомлений Azure.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 9dd6a66ea416ad61682b8e33c6163db3ac345d92
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447723"
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Диагностика пропущенных уведомлений в Центрах уведомлений

Один из наиболее распространенных вопросов от клиентов Центров уведомлений Azure — как исправить проблему, когда уведомления, отправленные из приложения, не отображаются на клиентских устройствах. Они хотят знать, где и почему уведомления были удалены и как это исправить. В этой статье мы рассмотрим, почему уведомления удаляются и не доходят до устройств. Вы узнаете, как анализировать и определить первопричину.

Для начала очень важно понять, как служба "Центры уведомлений" отправляет уведомления на устройство.

![Архитектура Центров уведомлений][0]

В типичном потоке отправки уведомлений сообщение отправляется из *серверной части приложения* в Центры уведомлений. Центры уведомлений обрабатывают все операции регистрации с учетом настроенных тегов и выражений тегов для определения "целей". Цели — это все операции регистрации, которые должны получить push-уведомление. Эти операции регистрации могут распространяться на любые из поддерживаемых нами платформ — iOS, Google, Windows, Windows Phone, Kindle и Baidu для China Android.

Определив цели, служба "Центры уведомлений" отправляет сообщения в *службу push-уведомлений* для платформы устройства. В качестве примера можно привести службу push-уведомлений Apple (APNs) для Apple и Firebase Cloud Messaging (FCM) для Google. Центры уведомлений отправляют сообщения, разделенные по нескольким пакетам регистраций. Они выполняют проверку подлинности в соответствующей службе push-уведомлений, исходя из учетных данных, установленных на портале Azure в разделе **настроек Центра уведомлений**. Затем служба push-уведомлений пересылает уведомления на соответствующие *клиентские устройства*.

Последним звеном доставки уведомлений является доставка между службой push-уведомлений платформы и устройством. Любой из четырех основных компонентов в процессе отправки push-уведомления (клиент, серверная часть приложения, Центры уведомлений и служба push-уведомлений платформы) может привести к удалению уведомления. Дополнительные сведения об архитектуре Центров уведомлений см. в [Концентраторы уведомлений Azure].

Сбой доставки уведомлений может произойти во время начального этапа тестирования или работы в промежуточной среде, что может указывать на проблему конфигурации. Если сбой доставки уведомлений происходит во время работы в рабочей среде, все или некоторые уведомления могут быть удалены, что указывает на некоторые более серьезные проблемы с приложением или шаблоном обмена сообщениями.

В следующем разделе рассматриваются сценарии удаления уведомлений, начиная с распространенных и заканчивая более редкими.

## <a name="notification-hubs-misconfiguration"></a>Неправильная настройка Центров уведомлений

Чтобы успешно отправлять уведомления в соответствующую службу push-уведомлений, служба "Центры уведомлений Azure" должна проходить аутентификацию в контексте приложения разработчика. Для этого разработчик создает учетную запись разработчика для соответствующей платформы (Google, Apple, Windows и т. д.). Затем он регистрирует свое приложение в платформе и получает учетные данные.

Учетные данные платформы необходимо добавить на портал Azure. Если уведомления не доходят до устройства, сначала следует убедиться, что в Центре уведомлений настроены правильные учетные данные. Они должны соответствовать данным приложения, созданного в учетной записи разработчика для конкретной платформы.

Пошаговые инструкции для выполнения этого процесса см. в статье [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows].

Ниже приведены некоторые распространенные сценарии неправильной настройки, которые следует проверить.

**Общие сведения**.

    * Убедитесь, что имя Центра уведомлений (без опечаток) одинаковое во всех следующих расположениях:
        * Где выполняется регистрация из клиента.
        * Где вы отправляете уведомления из серверной части.
        * Где вы настроили учетные данные службы push-уведомлений.
    * Убедитесь, что в клиенте и в серверной части приложения используются правильные строки конфигурации подписанного URL-адреса. Обычно необходимо использовать **DefaultListenSharedAccessSignature** на клиентском устройстве и **DefaultFullSharedAccessSignature** в серверной части приложения (что предоставляет разрешение для отправки уведомлений в Центры уведомлений).

**Конфигурация APNs**.

    You must maintain two different hubs: one hub for production, and another hub for testing. This means that you must upload the certificate that you use in a sandbox environment to a separate hub than the certificate and hub that you are going to use in production. Don't try to upload different types of certificates to the same hub. This might cause notification failures.

    If you inadvertently upload different types of certificates to the same hub, we recommend that you delete the hub and start fresh with a new hub. If for some reason you can't delete the hub, at a minimum, you must delete all the existing registrations from the hub.

**Конфигурация FCM**.

    1. Убедитесь, что *ключ сервера*, полученный из Firebase, совпадает с ключом сервера, зарегистрированным на портале Azure.

    ![Ключ сервера Firebase][3]

    2. Убедитесь, что на клиенте настроен **идентификатор проекта**. Значение для **идентификатора проекта** можно получить на панели мониторинга Firebase.

    ![Идентификатор проекта Firebase][1]

## <a name="application-issues"></a>Проблемы с приложением

**Теги и выражения тегов**.

    If you use tags or tag expressions to segment your audience, it's possible that when you send the notification, no target is found based on the tags or tag expressions that you specify in your send call.

    Review your registrations to ensure that there are matching tags when you send a notification. Then, verify the notification receipt only from the clients that have those registrations.

    As an example, if all your registrations with Notification Hubs were made by using the tag "Politics" and you send a notification with the tag "Sports," the notification isn't sent to any device. A complex case might involve tag expressions in which you registered by using "Tag A" OR "Tag B," but while sending notifications, you target "Tag A && Tag B." In the self-diagnosis tips section later in the article, we show you how to review your registrations and their tags.

**Проблемы с шаблонами. **

    If you use templates, ensure that you follow the guidelines described in [Templates].

**Недопустимые регистрации**.

    If the notification hub was configured correctly, and if any tags or tag expressions were used correctly, valid targets are found. Notifications should be sent to these targets. The Notification Hubs service then fires off several processing batches in parallel. Each batch sends messages to a set of registrations.

    > [!NOTE]
    > Because processing is performed in parallel, the order in which the notifications are delivered is not guaranteed.

    Notification Hubs is optimized for an "at-most once" message delivery model. We attempt deduplication, so that no notifications are delivered more than once to a device. To ensure this, we check registrations and ensure that only one message is sent per device identifier before the message is sent to the push notification service.

    As each batch is sent to the push notification service, which in turn is accepting and validating the registrations, it's possible that the push notification service will detect an error with one or more of the registrations in a batch. In this case, the push notification service returns an error to Notification Hubs, and the process stops. The push notification service drops that batch completely. This is especially true with APNS, which uses a TCP stream protocol.

    We are optimized for at-most once delivery. But in this case, the faulting registration is removed from the database. Then, we retry notification delivery for the rest of the devices in that batch.

    To get more error information about the failed delivery attempt against a registration, you can use the Notification Hubs REST APIs [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) and [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). For sample code, see the [Send REST example](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Проблемы службы push-уведомлений

Когда служба push-уведомлений платформы получит сообщение с уведомлением, она должна доставить уведомление на устройство. На этом этапе от службы "Центры уведомлений" больше ничего не зависит, и она не может контролировать время доставки и то, будет ли уведомление доставлено на устройство.

Так как службы уведомлений платформы надежны, как правило, уведомления доходят из службы push-уведомлений до устройства за несколько секунд. Если служба push-уведомлений регулирует доставку, Центры уведомлений реализуют стратегию экспоненциальной отсрочки. Если служба push-уведомлений недоступна в течение 30 минут, применяется политика, управляющая истечением срока действия и окончательным удалением этих сообщений.

Если служба push-уведомлений пытается доставить уведомление на устройство, находящееся в автономном режиме, она хранит уведомление ограниченный период времени. Уведомление доставляется на устройство, когда оно становится доступным.

Для каждого приложения хранится только одно последнее уведомление. Если при пребывании устройства в автономном режиме отправляется несколько уведомлений, отправка каждого нового уведомления приводит к предварительному удалению предыдущего. Сохранение только последнего уведомления называется *объединением уведомлений* в APNs и *разъединением* в FCM (с использованием ключа разъединения). Если устройство находится в автономном режиме в течение длительного времени, уведомления, сохраненные для него, удаляются. Дополнительные сведения см. в статье с [Обзор APNs] и [Сведения о сообщениях FCM].

С помощью Центров уведомлений Azure вы можете передать ключ объединения через заголовок HTTP, используя универсальный API SendNotification. Например, для пакета SDK для .NET можете использовать `SendNotificationAsync`. API SendNotification также принимает заголовки HTTP, которые передаются как есть в соответствующую службу push-уведомлений.

## <a name="self-diagnosis-tips"></a>Советы по самостоятельной диагностике

Ниже приведены способы диагностики первопричины пропущенных уведомлений в Центрах уведомлений.

### <a name="verify-credentials"></a>Проверка учетных данных

**Портал разработчика службы push-уведомлений**

Проверьте учетные данные на соответствующем портале разработчика службы push-уведомлений (APNs, FCM, служба уведомлений Windows и т. д.). Дополнительные сведения см. в статье [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows].

**Портал Azure**

Чтобы просмотреть и сопоставить учетные данные с данными, полученными на портале разработчика службы push-уведомлений, на портале Azure, перейдите на вкладку **Политики доступа**.

![Вкладка "Политики доступа" на портале Azure][4]

### <a name="verify-registrations"></a>Проверка регистраций

**Visual Studio**.

При использовании Visual Studio для разработки вы можете подключиться к Azure с помощью обозревателя сервера, чтобы просматривать множество служб Azure, включая Центры уведомлений, а также управлять ими. Это особенно удобно при работе в среде разработки или тестирования.

![Обозреватель серверов в Visual Studio.][9]

В центре можно просматривать все регистрации, а также управлять ими. Они классифицированы по таким категориям, как платформы, собственная или шаблонная регистрация, теги, идентификаторы службы push-уведомлений, идентификаторы регистрации и сроки действия. На этой странице можно также изменить регистрацию. Это особенно удобно для редактирования тегов.

![Регистрация устройств в Visual Studio][8]

> [!NOTE]
> Visual Studio следует использовать только при разработке или тестировании для ограниченного числа регистраций. Если возникает необходимость массового редактирования регистраций, можно воспользоваться функцией экспорта и импорта регистраций, описанной в статье [Практическое руководство. Массовый экспорт и изменение регистраций](https://msdn.microsoft.com/library/dn790624.aspx).

**Обозреватель служебной шины**.

Многие клиенты просматривают свои Центры уведомлений и управляют ими с помощью [Обозреватель служебной шины], который представляет собой проект с открытым кодом. Примеры кода см. на [Примеры кода Azure].

### <a name="verify-message-notifications"></a>Проверка уведомлений о сообщениях

 **Портал Azure**

Чтобы отправить тестовые уведомления клиентам, не запуская серверную часть службы, в разделе **Поддержка и устранение неполадок** выберите **Тестовая отправка**.

![Функциональные возможности тестовой отправки в Azure][7]

**Visual Studio**.

Тестовые уведомления также можно отправлять с помощью Visual Studio.

![Функциональные возможности тестовой отправки в Visual Studio][10]

Дополнительные сведения об использовании Центров уведомлений с помощью обозревателя серверов в Visual Studio см. в следующих статьях:

* [Просмотр регистраций устройства в центрах уведомлений]
* [Deep Dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3] (Подробный обзор. Релиз-кандидат Visual Studio 2013 с обновлением 2 и пакет Azure SDK 2.3)
* [Объявление о выпуске Visual Studio 2013 с обновлением 3 и пакета Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Уведомления о сбоях отладки и просмотр результатов уведомлений

**Свойство `EnableTestSend`**.

При отправке уведомления через Центры уведомлений сначала оно ставится в очередь для обработки. Центры уведомлений определяют правильные цели, а затем отправляют уведомление в службу push-уведомлений. При использовании REST API или любого клиентского пакета SDK удачное возвращение вызова отправки означает только то, что Центры уведомлений успешно поставили сообщение в очередь. При этом информация о том, что происходит после отправки сообщения из Центров уведомлений в службу push-уведомлений, не предоставляется.

Если уведомление не поступает на устройство клиента, возможно, во время попытки Центров уведомлений доставить сообщения в службу push-уведомлений произошла ошибка. Например, размер полезных данных может превышать максимально допустимый размер таких данных в службе push-уведомлений или настроенные в Центрах уведомлений учетные данные могут быть недоступными.

Информацию об ошибках службы push-уведомлений можно получить с помощью свойства [Сведения о свойстве EnableTestSend]. Оно автоматически включается при отправке тестового сообщения с портала или клиентского приложения Visual Studio и таким образом позволяет просматривать подробную отладочную информацию. Вы также можете использовать это свойство с помощью API. Сейчас его можно использовать в пакете SDK для .NET. В конечном счете оно будет добавлено ко всем клиентским пакетам SDK.

Чтобы использовать свойство `EnableTestSend` с вызовом REST, добавьте параметр строки запроса с именем *test* в конце вызова отправки. Например: 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Пример (пакет SDK для .NET)**.

Ниже приведен пример отправки собственного всплывающего уведомления при использовании пакета SDK для .NET:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

В конце выполнения `result.State` просто заявляет `Enqueued`. В результатах не будет предоставлена информация о вашем push-уведомлении.

Далее вы можете использовать логическое свойство `EnableTestSend`. С помощью свойства `EnableTestSend` во время инициализации `NotificationHubClient` можно получить подробные сведения об ошибках службы push-уведомлений, произошедших при отправке уведомления. Для возврата вызова отправки требуется дополнительное время, так как он выполняется только после доставки уведомления из Центров уведомлений в службу push-уведомлений, чтобы определить результат.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**Пример выходных данных**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Это сообщение указывает на то, что в Центрах уведомлений настроены недопустимые учетные данные или возникла проблема с регистрацией. Мы рекомендуем удалить эту регистрацию и дать клиенту создать ее заново, прежде чем отправлять сообщение.

> [!NOTE]
> Использование свойства `EnableTestSend` строго регулируется. Используйте его только в среде разработки и тестирования и с ограниченным набором регистраций. Мы отправляем уведомления об отладке только на 10 устройств. Кроме этого, у нас есть ограничения на количество обрабатываемых уведомлений об отладке — 10 в минуту.

### <a name="review-telemetry"></a>Просмотр телеметрии

**Использование портала Azure**.

На портале вы можете получить краткий обзор всех операций, выполняемых в Центре уведомлений.

1. На вкладке **Обзор** можно просмотреть объединенное представление регистраций, уведомлений и ошибок каждой платформы.

    ![Панель мониторинга "Обзор" в Центре уведомлений][5]

2. На вкладке **Монитор** можно добавить дополнительные метрики для конкретной платформы, чтобы получить подробные сведения о состоянии. Вы можете отдельно рассмотреть любые ошибки, связанные со службой push-уведомлений, возвращаемые, когда служба "Центры уведомлений" пытается отправить в нее уведомление.

    ![Журнал действий на портале Azure][6]

3. Сначала просмотрите **входящие сообщения**, **операции регистрации** и **доставленные уведомления**. Затем перейдите на вкладку каждой платформы, чтобы просмотреть ошибки, связанные со службой push-уведомлений.

4. Если параметры проверки подлинности для Центра уведомлений неверные, будет получена ошибка **выполнения проверки подлинности PNS**. Это сигнализирует о том, что следует проверить учетные данные службы push-уведомлений.

* **Программный доступ**

Дополнительные сведения о программном доступе см. в следующих статьях:

* [Программный доступ]  
* [Fetch Notification Hubs Telemetry Programmatically] (Получение данных телеметрии Центров уведомлений программным путем)

> [!NOTE]
> Некоторые функции, связанные с телеметрией, например экспорт и импорт данных регистраций и доступ к телеметрии с помощью API, доступны только на уровне службы "Стандартный". При попытке использовать эти функции на уровне службы "Бесплатный" или "Базовый" вы получите сообщение об исключении, если используется пакет SDK, и ошибку HTTP 403 (запрещено), если вы используете функции непосредственно из REST API.
>
> Чтобы использовать функции, связанные с телеметрией, сначала проверьте на портале Azure, что используется уровень службы "Стандартный".  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Концентраторы уведомлений Azure]: notification-hubs-push-notification-overview.md
[Начало работы с Центрами уведомлений для приложений универсальной платформы Windows]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Templates]: https://msdn.microsoft.com/library/dn530748.aspx
[Обзор APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Сведения о сообщениях FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Обозреватель служебной шины]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Примеры кода Azure]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Просмотр регистраций устройства в центрах уведомлений]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Deep Dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs (Подробный обзор. Релиз-кандидат Visual Studio 2013 с обновлением 2 и пакет Azure SDK 2.3)
[Объявление о выпуске Visual Studio 2013 с обновлением 3 и пакета Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[Сведения о свойстве EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Программный доступ]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Fetch Notification Hubs Telemetry Programmatically]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel (Получение данных телеметрии Центров уведомлений программным путем)
