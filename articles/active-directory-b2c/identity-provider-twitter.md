---
title: Настройка регистрации и входа с помощью учетной записи Twitter
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Twitter, используя Azure Active Directory B2C.
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
ms.openlocfilehash: e1161a8a4625c855198ea04f336d54c80271a3ef
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584700"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Twitter через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Предварительные требования

::: zone pivot="b2c-user-flow"

* [Создайте поток пользователя](tutorial-create-user-flows.md), чтобы пользователи могли зарегистрироваться и войти в ваше приложение.
* [Добавьте приложение веб-API приложения в клиент Azure Active Directory B2C](add-web-api-application.md), если это еще не сделано.

::: zone-end

::: zone pivot="b2c-custom-policy"

* Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).
* [Добавьте приложение веб-API приложения в клиент Azure Active Directory B2C](add-web-api-application.md), если это еще не сделано.

::: zone-end

## <a name="create-an-application"></a>Создание приложения

Чтобы использовать Twitter в качестве поставщика удостоверений в Azure AD B2C, необходимо создать приложение Twitter. Если у вас еще нет учетной записи Twitter, вы можете зарегистрироваться по адресу [https://twitter.com/signup](https://twitter.com/signup) .

1. Выполните вход на [веб-сайт разработчиков Twitter](https://developer.twitter.com/en/apps) с учетными данными для учетной записи Twitter.
1. Выберите  **создать приложение**.
1. Введите **имя приложения** и **описание приложения**.
1. В поле **Website URL** (URL-адрес веб-сайта) введите `https://your-tenant.b2clogin.com`. Замените `your-tenant` именем вашего клиента. Например, `https://contosob2c.b2clogin.com`.
1. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Замените `your-tenant` именем своего клиента, а `your-user-flow-Id` — идентификатором своего потока пользователя. Например, `b2c_1A_signup_signin_twitter`. При вводе имени клиента и идентификатора потока пользователя необходимо использовать все строчные буквы, даже если они определены с прописными буквами в Azure AD B2C.
1. В нижней части страницы прочтите условия использования и примите их, а затем щелкните **Create** (Создать).
1. На странице **сведений о приложении** выберите **Edit > Edit details** (Изменить > Изменить сведения), установите флажок в поле **Enable Sign in with Twitter** (Включить вход через Twitter), а затем нажмите кнопку **Save** (Сохранить).
1. Выберите **Keys and tokens** (Ключи и маркеры), а затем запишите значения **ключа API клиента** и **секретного ключа API клиента**, которые понадобятся далее.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **Twitter**.
1. Введите **Имя**. Например, *Twitter*.
1. В поле **идентификатор клиента** введите ключ API потребителя созданного ранее приложения Twitter.
1. В качестве **секрета клиента** введите записанный секретный ключ API-интерфейса потребителя.
1. Нажмите **Сохранить**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Создание ключа политики

Сохраните секретный ключ, который ранее был записан в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите фильтр **Каталог и подписка** в верхнем меню и выберите каталог, который содержит ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. На странице "Обзор" выберите **Identity Experience Framework**.
5. Выберите **Ключи политики**, а затем щелкните **Добавить**.
6. Для пункта **Параметры** выберите `Manual`.
7. Введите **имя** ключа политики. Например, `TwitterSecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
8. В поле **Секрет** введите ранее записанный секрет клиента.
9. Для параметра **Использование ключа** выберите `Encryption`.
10. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если необходимо разрешить пользователям входить в систему с помощью учетной записи Twitter, определите учетную запись, используемую в качестве поставщика утверждений, с которым Azure AD B2C может взаимодействовать через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Чтобы определить учетную запись Twitter в качестве поставщика утверждений, можно добавить ее в элемент **ClaimsProviders** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
2. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
3. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Замените значение параметра **client_id** ключом пользователя, записанным ранее.
5. Сохраните файл.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь вы настроили политику так, чтобы Azure AD B2C знала, как взаимодействовать с учетной записью Twitter. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще недоступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщика удостоверений Twitter.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если вы добавите для учетной записи Twitter элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `TwitterExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью Twitter для получения токена.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора используется то же значение, которое использовалось для **TargetClaimsExchangeId**.

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение идентификатора ранее созданного технического профиля. Например, `Twitter-OAUTH1`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Добавление поставщика удостоверений Twitter в поток пользователя 

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните поток пользователя, который вы хотите использовать для поставщика удостоверений Twitter.
1. В разделе **поставщики удостоверений социальных сетей** выберите **Twitter**.
1. Нажмите **Сохранить**.
1. Чтобы проверить политику, выберите пункт **выполнить пользовательскую последовательность**.
1. Для **приложения** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **выполнить поток пользователя**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInMSA.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInTwitter`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_twitter`.
1. Обновите значение атрибута **ReferenceId** для элемента **DefaultUserJourney**, чтобы он соответствовал идентификатору созданного вами пути взаимодействия пользователя (SignUpSignTwitter).
1. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
1. Убедитесь, что созданное приложение Azure AD B2C выбрано в поле **Выберите приложение**, а затем протестируйте его, щелкнув **Запустить сейчас**.

::: zone-end