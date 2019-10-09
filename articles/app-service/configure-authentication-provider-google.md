---
title: Настройка проверки подлинности Google в Службе приложений Azure
description: Узнайте, как настроить проверку подлинности Google для приложения службы приложений.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 917fa87a0cd0f7b0615a5139a7c15311f866739a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176968"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Настройка приложения службы приложений для использования имени входа Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этом разделе показано, как настроить службу приложений Azure для использования Google в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Регистрация приложения с помощью Google

1. Чтобы создать идентификатор клиента и секрет клиента, следуйте инструкциям в документации Google по [входу в Google для серверных приложений](https://developers.google.com/identity/sign-in/web/server-side-flow) . Нет необходимости вносить изменения в код. Просто используйте следующую информацию:
    - Для **полномочных источников JavaScript**используйте `https://<app-name>.azurewebsites.net` с именем приложения в *\<app-name >* .
    - Для **разрешения URI перенаправления**используйте `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Скопируйте идентификатор приложения и значения секрета приложения.

    > [!IMPORTANT]
    > Секрет приложения — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.

## <a name="secrets"> </a>Добавление данных Google в приложение

1. В [портал Azure]перейдите к приложению службы приложений.
1. Выберите **параметры** > **Проверка подлинности и авторизация**и убедитесь, что **Проверка подлинности службы приложений** **включена.**
1. Выберите **Google**, а затем вставьте значения в поле идентификатор приложения и секрет приложения, полученные ранее. Включите все области, необходимые для приложения.
1. Нажмите кнопку **ОК**.

   Служба приложений обеспечивает проверку подлинности, но не ограничивает разрешенный доступ к содержимому и API-интерфейсам сайта. Дополнительные сведения см. в разделе [Авторизация или отказ пользователей](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Используемых Чтобы ограничить доступ к сайту только для пользователей, прошедших проверку подлинности в Google, задайте **действие, которое будет выполняться, если запрос не прошел проверку подлинности** в **Google**. При установке этой функции приложение требует, чтобы все запросы прошли проверку подлинности. Он также перенаправляет все запросы без проверки подлинности в Google для проверки подлинности.

    > [!CAUTION]
    > Таким образом, ограниченный доступ применяется ко всем вызовам приложения, что может быть нежелательно для приложений, имеющих общедоступную домашнюю страницу, как во многих одностраничных приложениях. Для таких приложений рекомендуется **Разрешить анонимные запросы (без действий)** , чтобы приложение вручную начинало проверку подлинности. Дополнительные сведения см. в разделе [поток проверки подлинности](overview-authentication-authorization.md#authentication-flow).

1. Щелкните **Сохранить**.

Теперь вы готовы использовать Google для проверки подлинности в приложении.

## <a name="related-content"> </a>Дальнейшие действия

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[портал Azure]: https://portal.azure.com/

