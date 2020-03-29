---
title: Настройка регистрации в учетной записи учетной записи Майкрософт с помощью пользовательских политик
titleSuffix: Azure AD B2C
description: Как использовать пользовательские политики для включения учетной записи Майкрософт (MSA) в качестве поставщика идентификационных данных с помощью протокола OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188126"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа в Azure Active Directory B2C с помощью учетной записи Майкрософт с использованием пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье показано, как включить запись для пользователей учетной записи Майкрософт с помощью [пользовательских политик](custom-policy-overview.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).
- Если у вас еще нет учетной записи Майкрософт, создайте ее по адресу [https://www.live.com/](https://www.live.com/).

## <a name="register-an-application"></a>Регистрация приложения

Чтобы включить в систему пользователей с учетной записью Майкрософт, необходимо зарегистрировать приложение в рамках арендатора Azure AD. Клиент Azure AD отличается от клиента Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий ваш клиент Azure AD, выбрав фильтр **подписки каталога в** верхнем меню и выбрав каталог, содержащий арендатор Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Новая регистрация**.
1. Введите значение **Name** (Имя) для приложения. Например, *MSAapp1*.
1. Под **типами поддерживаемых учетных записей**выберите **учетные записи в любом каталоге организаций и личных учетных записей Майкрософт (например, Skype, Xbox, Outlook.com).**
1. Под **перенаправлениеuri (необязательно)**, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` выберите **веб** и введите в текстовое поле. Замените `your-tenant-name` имя арендатора Azure AD B2C.
1. Выберите **Регистр**
1. Запись **идентификатора приложения (клиента),** показанного на странице Обзор приложения. Это необходимо при настройке поставщика претензий в более позднем разделе.
1. Выберите **сертификаты & секреты**
1. Нажмите **Новый секрет клиента**
1. Введите **описание** для секрета, например *MSA Application Client Secret,* а затем нажмите **Добавить**.
1. Запись пароля приложения, отображаемого в столбце **Значение.** Это значение используется в следующем разделе.

## <a name="configuring-optional-claims"></a>Настройка необязательных утверждений

Если вы хотите `family_name` получить `given_name` и претензии от Azure AD, можно настроить дополнительные требования для приложения в uI или манифесте приложения портала Azure. Для получения дополнительной информации [см. Как предоставить дополнительные претензии к приложению Azure AD.](../active-directory/develop/active-directory-optional-claims.md)

1. Войдите на [портал Azure](https://portal.azure.com). Найдите и выберите **Azure Active Directory**.
1. Из раздела **«Управление»** выберите **регистрацию приложений.**
1. Выберите приложение, для которого вы хотите настроить дополнительные претензии в списке.
1. Из раздела **«Управление»** выберите **конфигурацию токенов (предварительный просмотр).**
1. Выберите **Добавить необязательный claim**.
1. Выберите тип маркера, который вы хотите настроить.
1. Выберите дополнительные претензии для добавления.
1. Нажмите кнопку **Добавить**.

## <a name="create-a-policy-key"></a>Создание ключа политики

Теперь, когда вы создали приложение в вашем клиентском клиенте Azure AD, вам необходимо хранить секрет клиента этого приложения в вашем клиентском клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог, содержащий ваш арендатор.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `MSASecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В **Secret**введите секрет клиента, который вы записали в предыдущем разделе.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Чтобы пользователи могли войти в систему с помощью учетной записи Майкрософт, необходимо определить учетную запись как поставщика претензий, с которым Azure AD B2C может общаться через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить Azure AD в качестве поставщика утверждений, добавив элемент **ClaimsProvider** в файл расширения политики.

1. Откройте файл политики *TrustFrameworkExtensions.xml.*
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
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. Замените значение **client_id** *идентификатором приложения (клиент* AD) Azure AD, который вы записывали ранее.
1. Сохраните файл.

Теперь вы настроили политику таким образом, чтобы Azure AD B2C знал, как общаться с вашим приложением учетной записи Майкрософт в Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Прежде чем продолжить, загрузите измененную политику, чтобы подтвердить, что у нее нет никаких проблем до сих пор.

1. Перейдите к своему арендатору Azure AD B2C на портале Azure и выберите **рамку опыта идентификации.**
1. На странице **пользовательских политик** выберите **пользовательскую политику Upload.**
1. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
1. Нажмите кнопку **Отправка**.

Если на портале нет ошибок, перейти в следующий раздел.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На данный момент вы настроили поставщика идентификационных данных, но он еще не доступен ни на одном из экранов регистрации или входа. Чтобы сделать его доступным, создайте дубликат существующего шаблона путешествия пользователя, а затем измените его так, чтобы он также имеет поставщика идентификационных данных учетной записи Майкрософт.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
1. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
1. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
1. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
1. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экране регистрации или входа. При добавлении элемента **ClaimsProviderSelection** для учетной записи Майкрософт, при попаской пользователя на страницу отображается новая кнопка.

1. В файле *TrustFrameworkExtensions.xml* найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
1. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В данном случае это действие предназначено для того, чтобы Azure AD B2C связывалась с учетной записью Майкрософт для получения токена.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
1. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора используется то же значение, которое использовалось для **TargetClaimsExchangeId**.

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Обновление значения **TechnicalProfileReferenceId** в соответствии `Id` с значением в элементе **TechnicalProfile** поставщика претензий, который вы добавили ранее. Например, `MSA-OIDC`.

1. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C происходит через приложение, которое регистрируется в вашем арендаторе B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInMSA.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInMSA`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_msa`.
1. Обновление значения атрибута **ReferenceId** в **DefaultUserJourney** в соответствии с идентификатором пользовательского путешествия, созданного ранее (SignUpSignInMSA).
1. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
1. Убедитесь, что приложение Azure AD B2C, созданное в предыдущем разделе (или выполнив предпосылки, например *webapp1* или *testapp1),* выбрано в поле **приложения Select,** а затем протестирует его, нажав **кнопку Run now.**
1. Выберите кнопку **учетной записи Майкрософт** и вопийте.

    Если операция ввместь прошла успешно, вы `jwt.ms` перенаправляется, на который отображается декодированный токен, похожий на:

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
