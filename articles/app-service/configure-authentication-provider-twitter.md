---
title: Настройка проверки подлинности Twitter в Службе приложений Azure
description: Узнайте, как настроить проверку подлинности Twitter для приложения службы приложений.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: d49b8bf9c62813023c1a1e06e0f8fc0d7809f48d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232051"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Как настроить приложение службы приложений для использования имени для входа Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этом разделе показано, как настроить службу приложений Azure для использования Twitter в качестве поставщика проверки подлинности.

Чтобы выполнить инструкции из этой статьи, вам потребуется номер телефона и учетная запись Twitter с проверенным электронным адресом. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Регистрация приложения в Twitter
1. Перейдите на [портал Azure]и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Он будет использоваться для настройки приложения Twitter.
2. Перейдите на веб-сайт [Twitter Developers] (Разработчики Twitter), войдите с помощью учетных данных учетной записи Twitter и щелкните **Create New App**(Создать приложение).
3. Введите **имя** и **описание** для нового приложения в соответствующих полях. Вставьте **URL-адрес** приложения в поле **Веб-сайт**. Затем в поле **URL-адрес обратного вызова**введите URL-адрес приложения службы приложений и добавьте путь `/.auth/login/aad/callback`к нему. Например, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Убедитесь, что используете схему HTTPS.
4. В нижней части страницы прочтите и примите условия. Затем щелкните **Создать приложение Twitter**. Отобразятся сведения о приложении.
5. Откройте вкладку **Параметры**, установите флажок **Allow this application to be used to sign in with Twitter** (Разрешить использовать это приложение для входа в Twitter), а затем щелкните **Обновить параметры**.
6. Откройте вкладку **Ключи и токены доступа** . Запишите значения полей **Consumer Key (API Key)** (Ключ потребителя (ключ API)) и **Consumer secret (API Secret)** (Секрет потребителя (секрет API)).
   
   > [!NOTE]
   > Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.
   > 
   > 

## <a name="secrets"> </a>Добавление данных Twitter в приложение
1. Снова вернитесь на [портал Azure] и перейдите к своему приложению. Щелкните **Параметры**, а затем — **Проверка подлинности или авторизация**.
2. Если функция аутентификации или авторизации не включена, установите переключатель в положение **Вкл**.
3. Щелкните **Twitter**. Вставьте значения идентификатора приложения и секрета приложения, полученные ранее. Затем нажмите кнопку **ОК**.
   
   ![][1]
   
   По умолчанию служба приложений обеспечивает проверку подлинности, но не ограничивает авторизованный доступ к содержимому сайта и API. Авторизация пользователей должна быть включена в код приложения.
4. (Необязательно.) Чтобы предоставить доступ к сайту только пользователям, прошедшим проверку подлинности в Twitter, установите для параметра **Предпринимаемое действие, если проверка подлинности для запроса не выполнена** значение **Twitter**. В этом случае все запросы, не прошедшие проверку подлинности, направляются для проверки подлинности с помощью Twitter.

> [!NOTE]
> Таким образом, ограниченный доступ применяется ко всем вызовам приложения, что может быть нежелательно для приложений, которым требуется общедоступная Домашняя страница, как во многих одностраничных приложениях. Для таких приложений рекомендуется **Разрешить анонимные запросы (без действий)** , если приложение вручную запускает вход, как описано [здесь](overview-authentication-authorization.md#authentication-flow).

5. Нажмите кнопку **Сохранить**.

Теперь вы можете использовать Twitter для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[портал Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
