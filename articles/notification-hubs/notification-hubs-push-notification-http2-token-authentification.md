---
title: Проверка подлинности на основе токенов (HTTP/2) для APNS в концентрах уведомлений Azure Документы Майкрософт
description: Узнайте, как использовать новую проверку подлинности маркеров для APNS.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263818"
---
# <a name="token-based-http2-authentication-for-apns"></a>Проверка подлинности на основе токенов (HTTP/2) для APNS

## <a name="overview"></a>Обзор

В этой статье объясняется, как использовать новый протокол APNS HTTP/2 с проверкой подлинности на основе токенов.

Основные преимущества использования нового протокола:

* Генерация токенов относительно проста (по сравнению с сертификатами)
* У токенов отсутствует срок окончания действия. Вы управляете их созданием и отзывом.
* Полезные данные могут иметь размер до 4 КБ.
* Синхронная обратная связь.
* Вы находитесь на последнем протоколе Apple - сертификаты по-прежнему используют двоичный протокол, который отмечен для амортизации

Использование этого нового механизма может быть выполнено в два этапа:

* Получить необходимую информацию на портале аккаунта Разработчика Apple.
* Наверстуйте свой концентратор уведомлений с новой информацией.

Концентраторы уведомлений теперь настроены на использование новой системы аутентификации с APNS.

Обратите внимание, что если вы мигрировали из использования учетных данных сертификатов для APNS, свойства маркеров перезаписывают ваш сертификат в нашей системе, но ваше приложение продолжает получать уведомления бесшовно.

## <a name="obtaining-authentication-information-from-apple"></a>Получение сведений о проверке подлинности от Apple

Для обеспечения аутентификации на основе токенов необходимо следующие свойства из учетной записи разработчика Apple:

### <a name="key-identifier"></a>Идентификатор ключа

Идентификатор ключа можно получить со страницы **Keys** в соответствии с **сертификатами, идентификаторами & профилями**в учетной записи разработчика Apple:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Идентификатор приложения и имя приложения

Имя приложения и идентификатор также доступны на странице **Сертификаты, Идентификаторы & профили** в учетной записи разработчика:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Наконфигурируется через портал .NET SDK или портал Azure

Вы можете настроить концентратор для использования аутентификации на основе токенов с помощью нашего [последнего клиентского SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)или на портале Azure. Чтобы включить проверку подлинности на основе токенов на портале, войдите на портал Azure и перейдите в настройки концентратора **уведомлений > панели Apple (APNS).** Выберите **токен** из свойства **режима аутентификации,** чтобы обновить концентратор со всеми соответствующими свойствами маркеров.

![Настройка токена](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Введите свойства, полученные из учетной записи разработчика Apple.
* Выберите режим приложения **(Производство** или **песочница).**
* Нажмите кнопку **«Сохранить»,** чтобы обновить учетные данные APNS.

Учетные данные на основе токенов состоят из следующих полей:

* **Ключевое идентификатор**: Идентификатор частного ключа, генерируемый на портале разработчиков Apple; например, `2USFGKSKLT`.
* **Идентификатор команды**: также называется "Префикс" или "App Префикс". Это идентификатор организации на портале разработчиков Apple; например, `S4V3D7CHJR`.
* **Комплект ID**: Также называется "Идентификатор приложения". Это идентификатор пакета для приложения; например, `com.microsoft.nhubsample2019`. Обратите внимание, что вы можете использовать один ключ для многих приложений. Это значение отображается в заголовке `apns-topic` HTTP при отправке уведомления и используется для таргетинга на конкретное приложение.
* **Токен**: Также называется "Ключ" или "Частный ключ". Это получено из файла .p8, созданного на портале разработчиков Apple. Ключ должен иметь APNS включен (который выбран на портале разработчика Apple при генерации ключа). Значение должно иметь PEM заголовок / петор раздели от него, когда вы поставляете его на NH портал / API.
* **Конечная точка**: Это переключение в лезвии портала концентраторов уведомлений и поле строки в API. Допустимые значения: `https://api.push.apple.com` или `https://api.sandbox.push.apple.com`. Концентраторы уведомлений используют это значение для среды производства или песочницы для отправки уведомлений. Это должно `aps-environment` соответствовать праву в приложении, в противном случае токены APNS, генерируемые, не соответствуют среде, а уведомления не отправляются.

Вот пример кода, иллюстрирующий правильное использование:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Дальнейшие действия

* [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md)
* [Настройка концентратора уведомлений на портале Azure](create-notification-hub-portal.md)
