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
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232134"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Как настроить приложение службы приложений для использования имени для входа Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этом разделе показано, как настроить службу приложений Azure для использования Google в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Регистрация приложения с помощью Google
1. Для создания идентификатора клиента и секрета клиента следуйте инструкциям в документации Google по [входу в Google для приложения на стороне сервера](https://developers.google.com/identity/sign-in/web/server-side-flow) , а также следующие сведения (нет необходимости вносить изменения в код):
    - Для **полномочных источников JavaScript**используйте `https://<app-name>.azurewebsites.net` с именем приложения в  *\<> App-Name*.
    - Для **разрешения URI перенаправления**используйте `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. После создания идентификатора клиента и секретов клиента скопируйте их значения.

    > [!IMPORTANT]
    > Секрет клиента — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.


## <a name="secrets"> </a>Добавление данных Google в приложение
1. Перейдите к своему приложению службы приложений на [портал Azure]. В меню слева выберите **Проверка подлинности и авторизация**.
2. Если функция аутентификации или авторизации не включена, установите переключатель в положение **Вкл**.
3. Щелкните **Google**. Вставьте значения идентификатора приложения и секрета приложения, полученные ранее, и при необходимости включите все области, необходимые для приложения. Затем нажмите кнопку **ОК**.

   Служба приложений обеспечивает проверку подлинности, но не ограничивает разрешенный доступ к содержимому и API-интерфейсам сайта. Дополнительные сведения см. в разделе [Авторизация или отказ пользователей](app-service-authentication-how-to.md#authorize-or-deny-users).
4. (Необязательно.) Чтобы предоставить доступ к сайту только пользователям, прошедшим проверку подлинности в Google, установите для параметра **Предпринимаемое действие, если проверка подлинности для запроса не выполнена** значение **Google**. В этом случае все запросы, не прошедшие проверку подлинности, направляются для проверки подлинности с помощью Google.

    > [!NOTE]
    > Таким образом, ограниченный доступ применяется ко всем вызовам приложения, что может быть нежелательно для приложений, которым требуется общедоступная Домашняя страница, как во многих одностраничных приложениях. Для таких приложений рекомендуется **Разрешить анонимные запросы (без действий)** , если приложение вручную запускает вход, как описано [здесь](overview-authentication-authorization.md#authentication-flow).
    
5. Нажмите кнопку **Сохранить**.

Теперь вы готовы использовать Google для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[портал Azure]: https://portal.azure.com/

