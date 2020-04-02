---
title: Как настроить приложение службы приложений для использования имени для входа Google
description: Узнайте, как настроить аутентификацию Google в качестве поставщика идентификационных данных для службы приложений или приложения Azure Functions.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519947"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Настройте приложение App Service или Azure Functions для использования входа в Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этой теме показано, как настроить службу приложений Azure или функции Azure для использования Google в качестве поставщика аутентификации.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать учетную запись Google, перейдите по ссылке [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Регистрация приложения с помощью Google

1. Следуйте документации Google [в Google Sign-In для приложений на стороне сервера](https://developers.google.com/identity/sign-in/web/server-side-flow) для создания идентификатора клиента и секрет клиента. Нет необходимости вносить какие-либо изменения в код. Просто используйте следующую информацию:
    - Для **авторизованных истоков JavaScript**используйте `https://<app-name>.azurewebsites.net` с именем вашего приложения в * \<>с именем приложения. *
    - Для **авторизованного перенаправления URI**используйте `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Копируйте идентификатор приложения и секретные значения приложения.

    > [!IMPORTANT]
    > Секрет приложения является важным учетным данным безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Добавление данных Google в приложение

1. На [портале Azure]перейдите в приложение Службы приложений.
1. Выберите **Настройки** > **Аутентификации / Авторизации**, и убедитесь, что проверка **подлинности службы приложений** находится **на**.
1. Выберите **Google**, затем вставить в App ID и App Secret значения, которые вы получили ранее. Включите все области, необходимые вашему приложению.
1. Щелкните **ОК**.

   Служба приложений обеспечивает аутентификацию, но не ограничивает авторизованный доступ к содержимому вашего сайта и AA. Для получения дополнительной информации [см.](app-service-authentication-how-to.md#authorize-or-deny-users)

1. (Необязательно) Чтобы ограничить доступ к сайту только для пользователей, удостоверяющих подлинность Google, установите **действие Action, когда запрос не проверен** **google.** При настройке этой функции приложение требует проверки подлинности всех запросов. Он также перенаправляет все непроверенные запросы в Google для проверки подлинности.

    > [!CAUTION]
    > Ограничение доступа таким образом распространяется на все вызовы в ваше приложение, что может оказаться нежелательным для приложений, имеющих общедоступную домашнюю страницу, как во многих одностраничных приложениях. Для таких приложений может быть предпочтительнее **разрешить анонимные запросы (без действия),** чтобы приложение вручную начало аутентификацию. Для получения дополнительной информации [см.](overview-authentication-authorization.md#authentication-flow)

1. Щелкните **Сохранить**.

Теперь вы готовы использовать Google для проверки подлинности в приложении.

## <a name="next-steps"></a><a name="related-content"> </a>Дальнейшие действия

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Портал Azure]: https://portal.azure.com/

