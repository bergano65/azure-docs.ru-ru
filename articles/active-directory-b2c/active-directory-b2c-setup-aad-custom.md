---
title: Настройка входа в Azure Active Directory B2C с помощью учетной записи Azure Active Directory с использованием пользовательских политик | Документация Майкрософт
description: Настройка входа в службу Azure Active Directory B2C с помощью учетной записи Azure Active Directory с использованием пользовательских политик.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9ad4a1a9fff8e011638eb7c532bf7619c87c1996
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785190"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа в Azure Active Directory B2C с помощью учетной записи Azure Active Directory с использованием пользовательских политик 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается включение входа для пользователей из организации Azure Active Directory (Azure AD) с помощью [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Технические условия

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Регистрация приложения

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте организации Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что используете каталог, содержащий клиент организации Azure AD (contoso.com), щелкнув **фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Регистрация нового приложения**.
5. Введите имя приложения. Например, `Azure AD B2C App`.
6. В качестве **типа приложения** выберите `Web app / API`.
7. В поле **URL-адрес входа** введите следующий URL-адрес в нижнем регистре, где `your-B2C-tenant-name` заменяется именем вашего клиента Azure AD B2C:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Например, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

8. Нажмите кнопку **Создать**. Скопируйте **идентификатор приложения** для использования в будущем.
9. Выберите приложение, а затем щелкните **Параметры**.
10. Выберите **Ключи**, введите описание ключа, установите срок действия и нажмите кнопку **Сохранить**. Скопируйте значение ключа, который отображается, для использования в будущем.

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить ключ приложения, который вы создали в клиенте Azure AD B2C.

1. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
2. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
3. На странице "Обзор" выберите **Identity Experience Framework**.
4. Выберите **Ключи политики**, а затем щелкните **Добавить**.
5. Для пункта **Параметры** выберите `Manual`.
6. Введите **имя** ключа политики. Например, `ContosoAppSecret`.  Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
7. В поле **Секрет** введите ранее записанный ключ приложения.
8. Для параметра **Использование ключа** выберите `Signature`.
9. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если необходимо разрешить пользователям входить в систему с помощью Azure Active Directory, нужно определить Azure Active Directory в качестве поставщика утверждений, с которым Azure AD B2C может взаимодействовать через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя. 

Вы можете определить Azure AD в качестве поставщика утверждений, добавив Azure AD к элементу **ClaimsProvider** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
2. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
3. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <OutputTokenFormat>JWT</OutputTokenFormat>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. В элементе **ClaimsProvider** укажите для **Domain** уникальное значение, позволяющее отличить этот поставщик удостоверений от других. Пример: `Contoso`. Не помещайте `.com` в конец этого параметра домена.
5. В элементе **ClaimsProvider** обновите значение **DisplayName**, указав понятное имя поставщика утверждений. В настоящее время это значение не используется.

### <a name="update-the-technical-profile"></a>Обновление технического профиля

Чтобы получить токен из конечной точки Azure AD, вам необходимо определить протоколы, используемые Azure AD B2C для взаимодействия с Azure AD. Этот выполняется в элементе **TechnicalProfile** в **ClaimsProvider**.

1. Обновите идентификатор элемента **TechnicalProfile**. Этот идентификатор используется для ссылки на этот технический профиль из других частей политики.
2. Обновите значение **DisplayName**. Это значение будет отображаться на кнопке входа на экране входа в систему.
3. Обновите значение **Description**.
4. Azure AD использует протокол OpenID Connect, поэтому для параметра **Protocol** должно быть задано значение `OpenIdConnect`.
5. Задайте для параметра **METADATA** значение `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`, где `your-AD-tenant-name` — это имя клиента Azure AD. Например: `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration` 
6. Откройте браузер и перейдите по URL-адресу **метаданных**, который вы только что обновили. Найдите для объект **issuer**, скопируйте и вставьте его в качестве значения **ProviderName** в XML-файле.
8. Задайте для параметров **client_id** и **IdTokenAudience** значение идентификатора приложения из регистрации приложения.
9. В разделе **CryptograhicKeys** измените значение **StorageReferenceId**, указав ключ политики, который был определен. Например, `ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

К этому моменту политика настроена, так что Azure AD B2C знает, как взаимодействовать с каталогом Azure AD. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений Azure AD.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экранах регистрации и входа. Если вы добавите для учетной записи Azure AD элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент под элементом **ClaimsProviderSelections**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Azure AD для получения токена. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений Azure AD.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для **Id** можно использовать то же значение, которое было использовано для **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```
    
    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение **Id** ранее созданного технического профиля. Например, `ContosoProfile`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C осуществляется с помощью приложения, созданного в вашем клиенте. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Щелкните **Приложения**, а затем выберите **Добавить**.
5. Задайте имя для приложения, например *testapp1*.
6. В пункте **Веб-приложение или веб-интерфейс API**выберите `Yes`, а затем в пункте **URL-адрес ответа** введите `https://jwt.ms`.
7. Нажмите кнопку **Создать**.

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInContoso.xml*.
2. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInContoso`.
3. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_contoso`.
4. Обновите значение атрибута **ReferenceId** для элемента **DefaultUserJourney**, чтобы он соответствовал идентификатору созданного вами пути взаимодействия пользователя (SignUpSignInContoso).
5. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
6. Убедитесь, что созданное приложение Azure AD B2C выбрано в поле **Выберите приложение**, а затем протестируйте его, щелкнув **Запустить сейчас**.

