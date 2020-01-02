---
title: Добавление push-уведомлений в приложение Android
description: Узнайте, как использовать мобильные приложения для отправки push-уведомлений в приложение Android.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ce4ebe9e8874e779b8da16e9c30fcfc3ca46754e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668610"
---
# <a name="add-push-notifications-to-your-android-app"></a>Добавление push-уведомлений в приложение Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы, необходимые для разработки мобильных приложений. Разработчики могут использовать службы **Build**, **Test** и **Distribute** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать его состояние и использование с помощью служб **Analytics** и **Diagnostics**, а также взаимодействовать с пользователями с помощью службы **Push**. Разработчики также могут использовать **Auth** для реализации проверки подлинности пользователей и службу **Data** для хранения и синхронизации данных приложений в облаке.
>
> Если вы хотите интегрировать облачные службы с мобильным приложением, зарегистрируйтесь в [Центре приложений](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) сегодня.

## <a name="overview"></a>Краткое описание

В этом учебнике мы добавим push-уведомления в [ознакомительный проект для платформы Android], чтобы при каждом добавлении новой записи на устройство отправлялось push-уведомление.

Если вы не используете скачанный проект сервера, необходимо добавить пакет расширений для push-уведомлений. Дополнительные сведения см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Технические условия

Кроме этого, вам потребуются:

* Интегрированная среда разработки, в зависимости от серверной части вашего проекта:

  * [Android Studio](https://developer.android.com/sdk/index.html) — если это приложение имеет серверную часть Node.js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) или более поздняя версия — если это приложение имеет серверную часть Microsoft .NET.
* Android 2.3 или более поздняя версия, репозиторий Google версии 27 или более поздняя версия и службы Google Play 9.0.2 или более поздняя версия для Firebase Cloud Messaging.
* Изучение [ознакомительный проект для платформы Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Создание проекта с поддержкой Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Настройка центра уведомлений

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Настройка Azure для отправки push-уведомлений

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Включение push-уведомлений для серверного проекта

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Добавьте в приложения возможность использования push-уведомлений

В этом разделе мы обновим клиентское приложение Android для обработки push-уведомлений.

### <a name="verify-android-sdk-version"></a>Проверка версии пакета SDK для Android

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Далее следует установить службы Google Play. Firebase Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте.

Если вы тестируете приложение на более старом устройстве, обратитесь к руководству [Добавление Firebase в проект Android] (Добавление Firebase в проект Android), чтобы определить, насколько малым можно задать это значение.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Добавление Firebase Cloud Messaging в проект

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Добавление кода

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Тестирование приложения с помощью опубликованной мобильной службы

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

## <a name="next-steps"></a>Дальнейшие действия

Вы изучили это руководство и теперь можете перейти к ознакомлению с одной из следующих тем.

* [Добавление аутентификации в приложение Android](app-service-mobile-android-get-started-users.md).
  Узнайте, как добавить аутентификацию в проект быстрого запуска ToDoList для Android с помощью поддерживаемого поставщика удостоверений.
* [Включение автономной синхронизации для приложения Android](app-service-mobile-android-get-started-offline-data.md).
  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильных приложений. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением (&mdash;просматривать, добавлять или изменять данные&mdash;) даже при отсутствии подключения к сети.

<!-- URLs -->
[ознакомительный проект для платформы Android]: app-service-mobile-android-get-started.md
[Добавление Firebase в проект Android]: https://firebase.google.com/docs/android/setup
