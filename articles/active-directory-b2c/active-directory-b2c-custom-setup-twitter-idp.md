---
title: Настройка входа с помощью учетной записи Twitter c использованием пользовательских политик Azure в Active Directory B2C | Документация Майкрософт
description: Настройка входа с помощью учетной записи Twitter с использованием пользовательских политик Azure в Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cc657e079949b8217031906efeb84049217d6493
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510248"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа с помощью учетной записи Twitter с использованием пользовательских политик Azure в Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается включение входа пользователей из учетных записей Twitter с помощью [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Технические условия

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Если у вас нет учетной записи Twitter, создайте ее на [странице регистрации Twitter](https://twitter.com/signup).

## <a name="create-an-application"></a>Создание приложения

Чтобы использовать Twitter в качестве поставщика удостоверений в Azure AD B2C, необходимо создать приложение Twitter.

1. Выполните вход на [веб-сайт разработчиков Twitter](https://developer.twitter.com/en/apps) с учетными данными для учетной записи Twitter.
2. Выберите **Create an app** (Создать приложение).
3. Введите **имя приложения** и **описание приложения**.
4. В поле **Website URL** (URL-адрес веб-сайта) введите `https://your-tenant.b2clogin.com`. Замените `your-tenant` именем вашего клиента. Например, https://contosob2c.b2clogin.com.
5. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`. Замените `your-tenant` именем своего клиента, а `your-policy-Id` — идентификатором своей политики. Например, `b2c_1A_signup_signin_twitter`. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами.
6. В нижней части страницы прочтите условия использования и примите их, а затем щелкните **Create** (Создать).
7. На странице **сведений о приложении** выберите **Edit > Edit details** (Изменить > Изменить сведения), установите флажок в поле **Enable Sign in with Twitter** (Включить вход через Twitter), а затем нажмите кнопку **Save** (Сохранить).
8. Выберите **Keys and tokens** (Ключи и маркеры), а затем запишите значения **ключа API клиента** и **секретного ключа API клиента**, которые понадобятся далее.

## <a name="create-a-policy-key"></a>Создание ключа политики

Сохраните секретный ключ, который ранее был записан в клиенте Azure AD B2C.

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите **фильтр каталога и подписки** в верхнем меню и выберите каталог, содержащий вашего клиента.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. На странице "Обзор" выберите **Identity Experience Framework — предварительная версия**.
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
3. Добавьте новый элемент **ClaimsProvider** следующим образом:

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

К этому моменту политика настроена так, что Azure AD B2C знает как взаимодействовать с учетной записью LinkedIn. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

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

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью Twitter для получения токена.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора используется то же значение, которое использовалось для **TargetClaimsExchangeId**.

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```
    
    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение идентификатора ранее созданного технического профиля. Например, `Twitter-OAUTH1`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C осуществляется с помощью приложения, созданного в вашем клиенте. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите **фильтр каталога и подписки** в верхнем меню и выберите каталог, содержащий вашего клиента.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Щелкните **Приложения**, а затем выберите **Добавить**.
5. Задайте имя для приложения, например *testapp1*.
6. В пункте **Веб-приложение или веб-интерфейс API**выберите `Yes`, а затем в пункте **URL-адрес ответа** введите `https://jwt.ms`.
7. Нажмите кнопку **Создать**.

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInMSA.xml*.
2. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInTwitter`.
3. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_twitter`
4. Обновите значение атрибута **ReferenceId** для элемента **DefaultUserJourney**, чтобы он соответствовал идентификатору созданного вами пути взаимодействия пользователя (SignUpSignTwitter).
5. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
6. Убедитесь, что созданное приложение Azure AD B2C выбрано в поле **Выберите приложение**, а затем протестируйте его, щелкнув **Запустить сейчас**.
