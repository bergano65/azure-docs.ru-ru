---
title: Настройка регистрации с учетной записью LinkedIn с помощью пользовательских политик
titleSuffix: Azure AD B2C
description: Настройка регистрации с учетной записью LinkedIn в Azure Active Directory B2C с помощью пользовательских политик.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188177"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа в Azure Active Directory B2C с помощью учетной записи LinkedIn с использованием пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье показано, как включить запись для пользователей из учетной записи LinkedIn с помощью [пользовательских политик](custom-policy-overview.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).
- LinkedIn учетная запись - Если у вас уже нет, [создайте учетную запись](https://www.linkedin.com/start/join).
- Страница LinkedIn - Вам нужна [страница LinkedIn,](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) чтобы связать ее с приложением LinkedIn, которое вы создаете в следующем разделе.

## <a name="create-an-application"></a>Создание приложения

Чтобы использовать LinkedIn в качестве поставщика удостоверений в Azure AD B2C, необходимо создать приложение LinkedIn.

### <a name="create-app"></a>Создание приложения

1. Выполните вход на веб-сайт [управления приложением LinkedIn](https://www.linkedin.com/secure/developer?newapp=) с учетными данными учетной записи LinkedIn.
1. Выберите **Создать приложение**.
1. Введите **имя приложения**.
1. Введите имя **компании,** соответствующее названию страницы LinkedIn. Создайте страницу LinkedIn, если у вас ее еще нет.
1. (Необязательно) Введите **URL-адрес политики конфиденциальности.** Он должен быть действительным URL, но не должен быть достижимой конечной точкой.
1. Введите **бизнес-электронную почту**.
1. Загрузите изображение **логотипа приложения.** Изображение логотипа должно быть квадратным, а его размеры должны быть не менее 100x100 пикселей.
1. Оставьте параметры по умолчанию в разделе **Продукты.**
1. Просмотрите информацию, представленную в **юридических терминах**. Если вы согласны с условиями, проверьте поле.
1. Выберите **Создать приложение**.

### <a name="configure-auth"></a>Настройка проверки подлинности

1. Выберите вкладку **Auth.**
1. Запись **идентификатора клиента**.
1. Выявить и записать **секрет клиента**.
1. В **настройках OAuth 2.0**добавьте следующий **URL-адрес Redirect.** Замените `your-tenant` именем вашего клиента. Используйте **все буквы нижнего регистра** для имени клиента, даже если оно определено буквами верхнего регистра в Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить секрет клиента, который ранее был записан в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог, содержащий ваш арендатор.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. На странице "Обзор" выберите **Identity Experience Framework**.
5. Выберите **ключи политики,** а затем выберите **Добавить**.
6. Для пункта **Параметры** выберите `Manual`.
7. Введите **имя** ключа политики. Например, `LinkedInSecret`. Префикс *B2C_1A_* автоматически добавляется к названию ключа.
8. В **Secret**введите секрет клиента, который вы ранее записали.
9. Для параметра **Использование ключа** выберите `Signature`.
10. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если необходимо разрешить пользователям входить в систему с помощью учетной записи LinkedIn, нужно определить учетную запись в качестве поставщика утверждений, с которым Azure AD B2C может взаимодействовать через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Определите учетную запись LinkedIn как поставщика претензий, добавив ее в элемент **ClaimsProviders** в файле расширения вашей политики.

1. Откройте в редакторе файл *SocialAndLocalAccounts/'TrustFrameworkExtensions.xml.* Этот файл входит в [стартовый пакет пользовательской политики,][starter-pack] который вы загрузили как часть одной из предпосылок.
1. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
1. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

1. Замените значение **client_id** идентификатором клиента приложения LinkedIn, которое вы ранее записали.
1. Сохраните файл.

### <a name="add-the-claims-transformations"></a>Добавление преобразований претензий

Технический профиль LinkedIn требует, чтобы **преобразования ExtractGivenNameFromLinkedInResponse** и **ExtractSurNameFromLinkedInResponse** потребовали, чтобы преобразования были добавлены в список возмещений претензий. Если в файле нет элемента **ClaimsTransformations,** определенного, добавьте элементы родительского XML, указанные ниже. Преобразования претензий также нуждаются в новом типе претензии, определенном под **названием nullStringClaim.**

Добавьте элемент **BuildingBlocks** в верхней части файла *TrustFrameworkExtensions.xml.* *См. пример TrustFrameworkBase.xml.*

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь у вас есть настроенная политика, чтобы Azure AD B2C знал, как общаться с вашей учетной записью LinkedIn. Попробуйте загрузить файл расширения вашей политики, чтобы подтвердить, что у него нет никаких проблем до сих пор.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Нажмите кнопку **Отправка**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На данный момент провайдер идентификационных данных настроен, но он не доступен ни на одном из экранов регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений LinkedIn.

1. Откройте файл *TrustFrameworkBase.xml* в стартовом пакете.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если вы добавите для учетной записи LinkedIn элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент под элементом **ClaimsProviderSelections**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В нашем примере действие — это взаимодействие Azure AD B2C с учетной записью LinkedIn для получения маркера.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора используется то же значение, которое использовалось для **TargetClaimsExchangeId**.

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Обновитите значение **TechnicalProfileReferenceId** в идентификатор технического профиля, созданный ранее. Например, `LinkedIn-OAUTH`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C происходит через приложение, которое регистрируется в вашем арендаторе B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInLinkedIn.xml*.
2. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInLinkedIn`.
3. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_linkedin`.
4. Обновите значение атрибута **ReferenceId** для элемента **DefaultUserJourney**, чтобы он соответствовал идентификатору созданного вами пути взаимодействия пользователя (SignUpSignLinkedIn).
5. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
6. Убедитесь, что созданное приложение Azure AD B2C выбрано в поле **Выберите приложение**, а затем протестируйте его, щелкнув **Запустить сейчас**.

## <a name="migration-from-v10-to-v20"></a>Миграция с v1.0 до v2.0

LinkedIn недавно [обновил свои AA с v1.0 до v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Чтобы перенести существующую конфигурацию в новую конфигурацию, используйте информацию в следующих разделах для обновления элементов технического профиля.

### <a name="replace-items-in-the-metadata"></a>Замена элементов в Метаданных

В существующем элементе **Метаданных** **TechnicalProfile**обновите следующие элементы **элемента** из:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

на:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Добавление элементов в Метаданные

В **метаданные** **TechnicalProfile**добавьте следующие элементы **пункта:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Обновление выходных данных

В существующих **outputClaims** of the **TechnicalProfile**обновитайте следующие элементы **OutputClaim** из:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

на:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Добавление новых элементов OutputClaimsTransformation

В **OutputClaimsTransformations** **technicalProfile,** добавить следующие **элементы OutputClaimsTransformation:**

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Определение новых преобразований претензий и типа претензий

На последнем этапе вы добавили новые преобразования требований, которые необходимо определить. Чтобы определить преобразования претензий, добавьте их в список **возмещений претензий.** Если в файле нет элемента **ClaimsTransformations,** определенного, добавьте элементы родительского XML, указанные ниже. Преобразования претензий также нуждаются в новом типе претензии, определенном под **названием nullStringClaim.**

Элемент **BuildingBlocks** должен быть добавлен в верхней части файла. В качестве примера можно просмотреть *TrustframeworkBase.xml.*

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Получение адреса электронной почты

В рамках миграции LinkedIn с v1.0 в v2.0 для получения адреса электронной почты требуется дополнительный звонок на другой API. Если вам необходимо получить адрес электронной почты во время регистрации, сделайте следующее:

1. Выполнить приведенные выше шаги, чтобы позволить Azure AD B2C свести на нет LinkedIn, чтобы пользователь встнул в систему. В рамках федерации Azure AD B2C получает токен доступа для LinkedIn.
2. Сохраните токен доступа LinkedIn в претензию. [Смотрите инструкции здесь](idp-pass-through-custom.md).
3. Добавьте следующие поставщик претензий, который `/emailAddress` делает запрос в API LinkedIn. Для авторизации этого запроса нужен токен доступа LinkedIn.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Добавьте следующий шаг оркестровки в путешествие пользователя, чтобы поставщик утверждений API запускался, когда пользователь подписывает сяпты в использовании LinkedIn. Убедитесь в `Order` том, чтобы обновить номер надлежащим образом. Добавьте этот шаг сразу после шага оркестровки, который запускает технический профиль LinkedIn.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Получение адреса электронной почты от LinkedIn во время регистрации является необязательным. Если вы решили не получать электронное письмо от LinkedIn, но требуете его во время регистрации, пользователь обязан вручную ввести адрес электронной почты и проверить его.

Полный выборец политики, использующий поставщика идентификационных данных LinkedIn, можно узнать в [пакете для начинающих по пользовательским политикам.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
