---
title: Настройка регистрации и входа с помощью учетной записи WeChat
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями WeChat при помощи Azure Active Directory B2C.
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
ms.openlocfilehash: 308cbc1e2a9d6e0fbce91b1fac5748af909cc2c1
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584635"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью WeChat в Azure Active Directory B2C


[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="create-a-wechat-application"></a>Создание приложения WeChat

Чтобы использовать учетную запись WeChat в качестве поставщика удостоверений в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас еще нет учетной записи WeChat, вы можете получить информацию по адресу [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>Регистрация приложения WeChat

1. Войдите в систему [https://open.weixin.qq.com/](https://open.weixin.qq.com/) , используя учетные данные WeChat.
1. Выберите **管理中心** (Центр управления).
1. Выполните отображаемые инструкции, чтобы зарегистрировать новое приложение.
1. Введите значение `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **授权回调域** (URL-адрес обратного вызова). Например, если имя клиента — contoso, задайте URL-адрес `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Скопируйте **идентификатор приложения** и **ключ приложения**. Эти значения потребуются при добавлении поставщика удостоверений для вашего клиента.

::: zone pivot="b2c-user-flow"

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Настройка WeChat в качестве поставщика удостоверений в клиенте

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **WeChat (Предварительная версия)**.
1. Введите **Имя**. Например, *WeChat*.
1. В поле **идентификатор клиента** введите идентификатор приложения WeChat, созданного ранее.
1. В качестве **секрета клиента** введите ЗАПИСАННЫЙ ключ приложения.
1. Нажмите **Сохранить**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить секрет клиента, который ранее был записан в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите фильтр **Каталог и подписка** в верхнем меню и выберите каталог, который содержит ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. На странице "Обзор" выберите **Identity Experience Framework**.
5. Выберите **Ключи политики**, а затем щелкните **Добавить**.
6. Для пункта **Параметры** выберите `Manual`.
7. Введите **имя** ключа политики. Например, `WeChatSecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
8. В поле **Секрет** введите ранее записанный секрет клиента.
9. Для параметра **Использование ключа** выберите `Signature`.
10. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если вы хотите, чтобы пользователи вошли с помощью учетной записи WeChat, необходимо определить учетную запись в качестве поставщика утверждений, который Azure AD B2C может взаимодействовать с помощью конечной точки. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить учетную запись WeChat в качестве поставщика утверждений, добавив ее в элемент **клаимспровидерс** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
2. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
3. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>wechat.com</Domain>
      <DisplayName>WeChat (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="WeChat-OAUTH">
          <DisplayName>WeChat</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">wechat</Item>
            <Item Key="authorization_endpoint">https://open.weixin.qq.com/connect/qrconnect</Item>
            <Item Key="AccessTokenEndpoint">https://api.weixin.qq.com/sns/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weixin.qq.com/sns/userinfo</Item>
            <Item Key="scope">snsapi_login</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="AccessTokenResponseFormat">json</Item>
            <Item Key="ClientIdParamName">appid</Item>
            <Item Key="ClientSecretParamName">secret</Item>
            <Item Key="ExtraParamsInAccessTokenEndpointResponse">openid</Item>
            <Item Key="ExtraParamsInClaimsEndpointRequest">openid</Item>
            <Item Key="ResponseErrorCodeParamName">errcode</Item>
            <Item Key="external_user_identity_claim_id">unionid</Item>
          <Item Key="client_id">Your WeChat application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeChatSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="unionid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="wechat.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Задайте для параметра **client_id** значение идентификатора приложения из регистрации приложения.
5. Сохраните файл.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь вы настроили политику так, чтобы Azure AD B2C знала, как взаимодействовать с вашей учетной записью WeChat. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, создайте дубликат существующего шаблона пути взаимодействия пользователя, а затем измените его, чтобы у него также был поставщик удостоверений WeChat.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInWeChat`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экранах регистрации и входа. Если добавить элемент **клаимспровидерселектион** для учетной записи WeChat, то при переходе пользователя на страницу появится новая кнопка.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `WeChatExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="WeChatExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. Действие, в данном случае, предназначено для Azure AD B2C для взаимодействия с учетной записью WeChat для получения маркера.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора можно использовать то же значение, которое было использовано для **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="WeChatExchange" TechnicalProfileReferenceId="WeChat-OAuth" />
    ```

    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение идентификатора ранее созданного технического профиля. Например, `WeChat-OAuth`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-wechat-identity-provider-to-a-user-flow"></a>Добавление поставщика удостоверений WeChat в поток пользователя 

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните пользовательский поток, который вы хотите использовать для поставщика удостоверений WeChat.
1. В разделе **поставщики удостоверений социальных сетей** выберите **WeChat**.
1. Нажмите **Сохранить**.
1. Чтобы проверить политику, выберите пункт **выполнить пользовательскую последовательность**.
1. Для **приложения** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **выполнить поток пользователя**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте его в *SignUpSignInWeChat.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInWeChat`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_WeChat`.
1. Обновите значение атрибута **ReferenceId** в **дефаултусержаурнэй** , чтобы оно совпадало с идентификатором нового пути взаимодействия пользователя, созданного пользователем (сигнупсигнвечат).
1. Сохраните изменения и отправьте файл.
1. В разделе **Настраиваемые политики** выберите **B2C_1A_signup_signin**.
1. В поле **выберите приложение** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Выберите **Запустить сейчас** и выберите WeChat, чтобы войти в систему с помощью WeChat и проверить настраиваемую политику.

::: zone-end
