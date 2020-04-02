---
title: Как настроить приложение службы приложений для использования имени для входа Twitter
description: Узнайте, как настроить аутентификацию Twitter в качестве поставщика идентификационных данных для приложения Службы приложений или приложения Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519908"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Настройте приложение App Service или Приложение Azure Functions для использования входа в Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этой статье показано, как настроить службу приложений Azure или функции Azure для использования Twitter в качестве поставщика аутентификации.

Для завершения процедуры в этой статье, вам нужно щебетать счета, который имеет проверенный адрес электронной почты и номер телефона. Чтобы создать учетную запись Twitter, перейдите по ссылке [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Регистрация приложения в Twitter

1. Войдите на [портал Azure] и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Вы будете использовать его для настройки приложения Twitter.
1. Перейдите на веб-сайт [разработчиков Twitter,] войдите в систему с учетными данными учетной записи Twitter и выберите **Создать приложение.**
1. Введите **имя приложения** и **описание приложения** для вашего нового приложения. Вставьте **URL-адрес** приложения в поле **URL-адреса веб-сайта.** В разделе **URL-адресов Callback** введите URL HTTPS приложения `/.auth/login/twitter/callback`App Service и прикажите путь. Например, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. В нижней части страницы, введите по крайней мере 100 символов в **Расскажите нам, как это приложение будет использоваться,** а затем выберите **Создать**. Нажмите **Создать** снова во всплывающем окно. Детали приложения отображаются.
1. Откройте вкладку **Ключи и токены доступа** .

   Обратите внимание на эти значения:
   - Ключ API
   - Секретный ключ API

   > [!NOTE]
   > Секретный ключ API является важным учетным данным безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Добавление данных Twitter в приложение

1. Зайдите в приложение на [портале Azure].
1. Выберите **Настройки** > **Аутентификации / Авторизации**, и убедитесь, что проверка **подлинности службы приложений** находится **на**.
1. Выберите **Twitter**.
1. Вставить и `API key` `API secret key` значения, которые вы получили ранее.
1. Щелкните **ОК**.

   ![Скриншот настроек Мобильного Приложения Twitter][1]

   По умолчанию Служба App предоставляет аутентификацию, но не ограничивает авторизованный доступ к содержимому вашего сайта и AA. Авторизация пользователей должна быть включена в код приложения.

1. (Необязательно.) Чтобы предоставить доступ к сайту только пользователям, прошедшим проверку подлинности в Twitter, установите для параметра **Предпринимаемое действие, если проверка подлинности для запроса не выполнена** значение **Twitter**. При настройке этой функции приложение требует проверки подлинности всех запросов. Он также перенаправляет все непроверенные запросы в Twitter для проверки подлинности.

   > [!CAUTION]
   > Ограничение доступа таким образом распространяется на все вызовы в ваше приложение, что может оказаться нежелательным для приложений, имеющих общедоступную домашнюю страницу, как во многих одностраничных приложениях. Для таких приложений может быть предпочтительнее **разрешить анонимные запросы (без действия),** чтобы приложение вручную начало аутентификацию. Для получения дополнительной информации [см.](overview-authentication-authorization.md#authentication-flow)

1. Щелкните **Сохранить**.

Теперь вы можете использовать Twitter для проверки подлинности в приложении.

## <a name="next-steps"></a><a name="related-content"> </a>Дальнейшие действия

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Портал Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
