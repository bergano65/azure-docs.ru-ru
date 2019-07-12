---
title: Добавление утверждений и настройка пользовательского ввода с помощью настраиваемых политик в Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как настраивать пользовательский ввод и использовать утверждения при регистрации или входе в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae6d55180785c9407662776a95fcba31f8ba5275
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835210"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Добавление утверждений и настройка пользовательского ввода с помощью настраиваемых политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описано, как использовать новую запись, предоставляемую пользователем (утверждение), при регистрации пользователя в Azure Active Directory (Azure AD) B2C.  Вы настроите запись как раскрывающийся список и определить, является ли это обязательно.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md). Проверьте процесс регистрации или входа пользователя для регистрации новой локальной учетной записи, прежде чем продолжить.

## <a name="add-claims"></a>Добавление утверждений

При регистрации или входе пользователя собираются соответствующие исходные данные. Дополнительные утверждения можно собрать позже при редактировании профиля. Каждый раз, когда Azure AD B2C собирает сведения непосредственно от пользователя интерактивном режиме, инфраструктура процедур идентификации использует его самоподтвержденным поставщиком.


### <a name="define-the-claim"></a>Определение утверждения

Давайте попросим пользователя свой Город. Добавьте следующий элемент в элемент **ClaimsSchema** в файле политики TrustFrameworkBase:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Для определения утверждения используются такие элементы:

- **DisplayName** — это строка, определяющая пользовательскую метку.
- **UserHelpText** — позволяет пользователю узнать требования.
- **UserInputType** — может быть текстовым полем, переключателем, раскрывающимся списком или меню для выбора нескольких элементов.

#### <a name="textbox"></a>TextBox

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

![Элемент управления dropdown одиночного выбора, отображаются несколько вариантов](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

![Элемент управления checkbox множественного выбора, отображаются несколько вариантов](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="add-the-claim-to-the-user-journey"></a>Использование утверждения в операциях, выполняемых пользователем

1. Добавьте утверждение в виде `<OutputClaim ClaimTypeReferenceId="city"/>` в технический профиль `LocalAccountSignUpWithLogonEmail` в файле политики TrustFrameworkBase. Этот технический профиль использует SelfAssertedAttributeProvider.

    ```xml
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
      </CryptographicKeys>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
        <!-- Optional claims, to be collected from the user -->
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surName" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

2. Добавьте утверждение в технический профиль AAD-UserWriteUsingLogonEmail в виде `<PersistedClaim ClaimTypeReferenceId="city" />` для записи утверждения в каталог AAD после его получения у пользователя. Этот шаг можно пропустить, если вы не хотите сохранять утверждение в каталог для использования в будущем.

    ```xml
    <!-- Technical profiles for local accounts -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <!-- Required claims -->
        <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
        <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
        <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
        <!-- Optional claims. -->
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />
        <PersistedClaim ClaimTypeReferenceId="city" />
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

3. Добавьте утверждение `<OutputClaim ClaimTypeReferenceId="city" />` в технический профиль, который выполняет чтение из каталога при входе пользователя.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Required claims -->
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

4. Добавьте утверждение `<OutputClaim ClaimTypeReferenceId="city" />` в файл SignUporSignIn.xml, чтобы это утверждение отправлялось в приложение в токене после успешной операции.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="identityProvider" />
          <OutputClaim ClaimTypeReferenceId="city" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что используете каталог, содержащий клиент организации Azure AD, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, который содержит ваш клиент Azure AD.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Identity Experience Framework (предварительная версия)** .
5. Выберите **Отправка пользовательской политики** и отправьте два файла политики, которые вы изменили ранее.
2. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
3. Вы сможете зарегистрироваться, используя адрес электронной почты.

Экран регистрации должен выглядеть примерно так:

![Снимок экрана с измененным вариантом регистрации](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

Токен, отправленный обратно в ваше приложение, включает утверждение `city`.

```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification"></a>Необязательно: отключение проверки по электронной почте

Чтобы пропустить проверку по электронной почте, можно удалить `PartnerClaimType="Verified.Email"`. В таком случае адрес электронной почты нужно будет указывать, но он не будет проверяться, если не удалить фрагмент Required = true.  Подумайте, подходит ли этот вариант в вашем случае.

Проверенная электронная почта по умолчанию включается в `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` в файле политики TrustFrameworkBase:

```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Следующие шаги

Узнайте, как [использовать настраиваемые атрибуты в пользовательской политике изменения профиля](active-directory-b2c-create-custom-attributes-profile-edit-custom.md).
