---
title: Добавление push-уведомлений в приложение Android с помощью мобильных приложений | Документация Майкрософт
description: Узнайте, как использовать мобильные приложения для отправки push-уведомлений в приложение Android.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: elamalani
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 529aa8327d31cdda044178b6d03035b602744db2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443658"
---
# <a name="add-push-notifications-to-your-android-app"></a>Добавление push-уведомлений в приложение Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Центр приложений Visual Studio вкладывает средства в новые и интегрированной службы, необходимые для разработки мобильных приложений. Разработчики могут использовать **построения**, **теста** и **распределить** служб для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения, разработчики могут отслеживать состояние и использования их приложений с помощью **Analytics** и **диагностики** служб и общайтесь с пользователями, с помощью **Push** Служба. Разработчики также могут использовать **Auth** подлинность пользователей и **данных** службы для сохранения и синхронизировать данные приложения в облаке. Ознакомьтесь с [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-push) уже сегодня.
>

## <a name="overview"></a>Обзор

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

## <a name="add-push-notifications-to-your-app"></a>Добавление push-уведомлений в приложение

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
