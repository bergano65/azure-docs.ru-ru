---
title: Настройка регистрации и входа с помощью учетной записи Facebook
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Facebook, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 37fc33ae8084a2b4e99e7b5dc417eac70060eef5
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516192"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Facebook через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Создание приложения Facebook

Чтобы использовать учетную запись Facebook в качестве [поставщика удостоверений](authorization-code-flow.md) в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас нет учетной записи Facebook, вы можете зарегистрироваться здесь: [https://www.facebook.com/](https://www.facebook.com/).

1. Выполните вход на [веб-сайт разработчиков Facebook](https://developers.facebook.com/) с учетными данными от учетной записи Facebook.
1. Если вы этого еще не сделали, необходимо зарегистрироваться в качестве разработчика Facebook. Для этого щелкните **Get Started** (Начать работу) в правом верхнем углу страницы, примите политики Facebook и завершите регистрацию.
1. Щелкните **My Apps** (Мои приложения) и **Create App** (Создать приложение).
1. Выберите **сборка с подключением**.
1. Введите **Отображаемое название** и **Эл. адрес для связи**.
1. Щелкните **Create App ID** (Создать идентификатор приложения). Для этого может потребоваться принять условия политики платформы Facebook и пройти проверку безопасности в сети.
1. Выберите **Параметры** > **Основные**.
    1. Выберите **категорию**, например `Business and Pages`. Это значение требуется Facebook, но не используется для Azure AD B2C.
    1. Введите URL-адрес для **терминов URL службы**, например `http://www.contoso.com/tos` . URL-адрес политики — это страница, которую вы поддерживаете для предоставления условий для приложения.
    1. Введите **URL-адрес политики конфиденциальности**, например `http://www.contoso.com/privacy`. URL-адрес политики — это управляемая вами страница, где предоставляются сведения о конфиденциальности для вашего приложения.
1. В нижней части страницы, выберите **Добавить платформу**, а затем выберите **Веб-сайт**.
1. В поле **URL-адрес сайта** введите адрес веб-сайта, например `https://contoso.com` . 
1. Щелкните **Save changes** (Сохранить изменения).
1. В верхней части страницы скопируйте значение **APP ID** (Идентификатор приложения).
1. Щелкните **Show** (Показать) и скопируйте значение **App Secret** (Секрет приложения). Оба значения потребуются вам для настройки Facebook в качестве поставщика удостоверений для вашего клиента. **Секрет приложения** — это важные учетные данные безопасности.
1. В меню щелкните знак « **плюс»** рядом с пунктом **товары**. В разделе **Добавление продуктов в приложение** выберите **Настройка** в поле **имя входа Facebook**.
1. В меню выберите **имя входа Facebook** и щелкните **Параметры**.
1. В поле **Valid OAuth redirect URIs** (Допустимые URI перенаправления OAuth) введите `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Замените `your-tenant-name` именем вашего клиента. Щелкните **Save Changes** (Сохранить изменения) в нижней части страницы.
1. Чтобы сделать приложение Facebook доступным для Azure AD B2C, щелкните селектор состояния в правом верхнем углу страницы и **включите его,** чтобы сделать приложение общедоступным, а затем выберите **режим переключения**.  На этом этапе состояние должно измениться с **Разработка** на **Активно**.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Настройка учетной записи Facebook в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Щелкните элемент **Поставщики удостоверений** и выберите вариант **Facebook**.
1. Введите **Имя**. Например, *Facebook*.
1. В поле **Идентификатор клиента** введите идентификатор созданного ранее приложения Facebook.
1. В поле **Секрет клиента** введите секрет приложения, записанный ранее.
1. Щелкните **Сохранить**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-facebook-as-an-identity-provider"></a>Добавление Facebook в качестве поставщика удостоверений

1. В файле `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** замените значение `client_id` идентификатором приложения Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Добавление поставщика удостоверений Facebook в поток пользователя 

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните пользовательский поток, который вы хотите использовать для поставщика удостоверений Facebook.
1. В разделе **поставщики удостоверений социальных сетей** выберите **Facebook**.
1. Щелкните **Сохранить**.
1. Чтобы проверить политику, выберите пункт **выполнить пользовательскую последовательность**.
1. Для **приложения** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **выполнить поток пользователя**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="upload-and-test-the-policy"></a>Отправка и тестирование политики

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Отправьте файл *TrustFrameworkExtensions.xml* в клиент.
1. В разделе **Настраиваемые политики** выберите **B2C_1A_signup_signin**.
1. В поле **выберите приложение** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **Запустить сейчас** и выберите Facebook, чтобы войти с учетной записью Facebook и проверить работу настраиваемой политики.

::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [передать маркер Facebook в приложение](idp-pass-through-user-flow.md).
