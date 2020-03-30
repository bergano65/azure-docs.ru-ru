---
title: Добавление аутентификации на Android
description: Узнайте, как использовать службу приложений Azure для проверки подлинности пользователей вашего приложения Android с помощью поставщиков идентификационных данных, таких как Google, Facebook, Twitter и Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461646"
---
# <a name="add-authentication-to-your-android-app"></a>Добавление проверки подлинности в приложение Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Сводка
В этом руководстве вы добавите аутентификацию в проект быстрого запуска ToDoList для Android с помощью поддерживаемого поставщика удостоверений. Этот учебник создан на основе учебника [Начало работы с мобильными службами] , который необходимо изучить в первую очередь.

## <a name="register-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Регистрация приложения для аутентификации и настройка службы приложений Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Добавление приложения в список разрешенных URL-адресов внешнего перенаправления

Для безопасной аутентификации требуется определить новую схему URL-адресов для своего приложения. Это позволяет системе аутентификации выполнять перенаправление обратно в приложение после завершения процесса аутентификации. В этом руководстве мы повсеместно используем схему URL-адресов _appname_. Тем не менее можно использовать любую схему URL-адресов на свой выбор. Она должна быть уникальной для мобильного приложения. Вот как можно включить перенаправление на стороне сервера.

1. На [портале Azure] выберите свою службу приложений.

2. Выберите пункт меню **Аутентификация или авторизация**.

3. В поле **Разрешенные URL-адреса внешнего перенаправления** введите `appname://easyauth.callback`.  _appname_ в этой строке — это схема URL-адресов для вашего мобильного приложения.  Она должна соответствовать обычной спецификации URL-адресов для протокола (можно использовать буквы и цифры, и адрес должен начинаться с буквы).  Необходимо записать выбранную строку, так как потребуется в нескольких местах настроить код мобильного приложения с использованием схемы URL-адресов.

4. Нажмите кнопку **ОК**.

5. Нажмите **Сохранить**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* В Android Studio откройте проект, при выполнении заданий руководства [Начало работы с мобильными службами]. В меню **Run** (Запуск) щелкните **Run app** (Запустить приложение). Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния "401 (Не авторизовано)".

     Это происходит, потому что приложение пытается получить доступ к серверной части как неаутентифицированный пользователь, а таблица *TodoItem* теперь требует проходить аутентификацию.

Далее мы изменим приложение таким образом, что оно станет аутентифицировать пользователей, прежде чем запрашивать ресурсы из серверной части мобильных приложений.

## <a name="add-authentication-to-the-app"></a>Добавление проверки подлинности в приложение
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Кэширование маркеров проверки подлинности на клиенте
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Дальнейшие действия
Вы прошли этот учебник по обычной проверке подлинности и теперь можете перейти к одному из следующих учебников:

* [Добавьте push-уведомления в ваше приложение для Android.](app-service-mobile-android-get-started-push.md)
  Узнайте, как добавить поддержку push-уведомлений в мобильное приложение и настроить в его серверной части использование центров уведомлений Azure для отправки push-уведомлений.
* [Включение автономной синхронизации для приложения Android](app-service-mobile-android-get-started-offline-data.md).
  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильных приложений. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением (&mdash;просматривать, добавлять или изменять данные&mdash;) даже при отсутствии подключения к сети.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Начало работы с мобильными приложениями]: app-service-mobile-android-get-started.md
[Портал Azure]: https://portal.azure.com/
