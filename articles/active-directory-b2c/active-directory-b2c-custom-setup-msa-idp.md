---
title: Добавить учетную запись Майкрософт (MSA) в качестве поставщика удостоверений с помощью настраиваемых политик в Azure Active Directory B2C
description: Пример использования Майкрософт в качестве поставщика удостоверений с помощью протокола OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654153"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа в Azure Active Directory B2C с помощью учетной записи Майкрософт с использованием пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается включение входа пользователей из учетных записей Майкрософт с помощью [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Создайте учетную запись Майкрософт на сайте [https://www.live.com/](https://www.live.com/), если у вас ее еще нет.

## <a name="add-an-application"></a>Добавление приложения

Чтобы включить вход для пользователей с учетной записью Майкрософт, вам потребуется зарегистрировать приложение в клиенте Azure AD. Клиент Azure AD отличается от клиента Azure AD B2C.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Убедитесь, что используете каталог, содержащий клиент организации Azure AD, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, который содержит ваш клиент Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Новая регистрация**.
1. Введите значение **Name** (Имя) для приложения. Например, *MSAapp1*.
1. В разделе **поддерживаемые типы учетных записей**выберите **учетные записи в любой каталог организации и личных учетных записей Майкрософт (например Скайп, Xbox, Outlook.com)** .
1. В разделе **URI перенаправления (необязательно)** выберите **Web** и введите `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в текстовом поле. Замените `your-tenant-name` именем своего клиента Azure AD B2C.
1. Выберите **регистрации**
1. Запись **идентификатор приложения (клиент)** показано на странице обзора приложения. Это необходимо при настройке поставщика утверждений в одном из следующих разделов.
1. Выберите **сертификаты и секреты**
1. Нажмите кнопку **новый секрет клиента**
1. Введите **описание** секрета, например *секрет клиента приложения MSA*, а затем нажмите кнопку **добавить**.
1. Запишите пароль приложения, указанный в **значение** столбца. Это значение используется в следующем разделе.

## <a name="create-a-policy-key"></a>Создание ключа политики

Теперь, когда вы создали приложение в клиенте Azure AD, необходимо сохранить секрет клиента приложения в клиенте Azure AD B2C.

1. Войдите на [портале Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите **фильтр каталога и подписки** в верхнем меню и выберите каталог, содержащий вашего клиента.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `MSASecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В **секрет**, введите секрет клиента, которые были записаны в предыдущем разделе.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Чтобы позволить пользователям входить с помощью учетной записи Майкрософт, необходимо определить учетную запись в качестве поставщика утверждений Azure AD B2C для взаимодействия с через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить Azure AD в качестве поставщика утверждений, добавив элемент **ClaimsProvider** в файл расширения политики.

1. Откройте *TrustFrameworkExtensions.xml* файл политики.
1. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
1. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Замените значение **client_id** с приложением Azure AD *идентификатор приложения (клиент)* , записанным ранее.
1. Сохраните файл.

Вы настроили политики теперь таким образом, чтобы Azure AD B2C знает, как взаимодействовать с приложением учетной записи Майкрософт в Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Прежде чем продолжить, отправьте измененную политику, чтобы убедиться, что все проблемы до сих.

1. Перейдите к клиенту Azure AD B2C в Azure, портала и выберите **Identity Experience Framework**.
1. На **пользовательских политик** выберите **отправки настраиваемой политики**.
1. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
1. Щелкните **Отправить**.

Если ошибки не отображаются на портале, перейдите к следующему разделу.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе вы настроили поставщик удостоверений, но он еще не доступен в любом из экранов регистрации или входа в систему. Чтобы сделать его доступным, создать дубликат существующего шаблона пути пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений учетной записи Майкрософт.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
1. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
1. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
1. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
1. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если вы добавите **ClaimsProviderSelection** элемент для учетной записи Майкрософт, новая кнопка отображается при переходе пользователя на странице.

1. В файле *TrustFrameworkExtensions.xml* найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
1. Добавьте следующий элемент для параметра **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия с учетной записью Майкрософт, чтобы получить маркер Azure AD B2C.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
1. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора используется то же значение, которое использовалось для **TargetClaimsExchangeId**.

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Обновите значение **TechnicalProfileReferenceId** должны совпадать с `Id` значение в **TechnicalProfile** элемент поставщика утверждений, добавленный ранее. Например, `MSA-OIDC`.

1. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

С помощью приложения, созданный в вашем клиенте Azure AD B2C осуществляется связь с Azure AD B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите **фильтр каталога и подписки** в верхнем меню и выберите каталог, содержащий вашего клиента.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. Щелкните **Приложения**, а затем выберите **Добавить**.
1. Задайте имя для приложения, например *testapp1*.
1. В пункте **Веб-приложение или веб-интерфейс API**выберите `Yes`, а затем в пункте **URL-адрес ответа** введите `https://jwt.ms`.
1. Нажмите кнопку **Создать**.

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInMSA.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInMSA`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_msa`.
1. Обновите значение **ReferenceId** атрибут в **DefaultUserJourney** идентификатор пути взаимодействия пользователя, что вы создали ранее (SignUpSignInMSA).
1. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
1. Убедитесь, что это приложение Azure AD B2C, созданный в предыдущем разделе (или путем выполнения предварительных требований, например *webapp1* или *testapp1*) выбран в **выберите приложение** поле, а затем проверить его, нажав кнопку **запустить сейчас**.
1. Выберите **учетной записи Майкрософт** кнопку и войдите в систему.

    При успешном выполнении операции входа вы будете перенаправлены `jwt.ms` отображает декодировать Token, аналогичную:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
