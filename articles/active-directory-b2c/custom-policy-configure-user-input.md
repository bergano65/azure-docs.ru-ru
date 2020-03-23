---
title: Добавление утверждений и настройка пользовательского ввода в пользовательских политиках
titleSuffix: Azure AD B2C
description: Узнайте, как настраивать пользовательский ввод и использовать утверждения при регистрации или входе в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56a3478f1c0dbc05eba07a5109f5bb6ba89b79d0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079880"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Добавление утверждений и настройка пользовательского ввода с помощью настраиваемых политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье вы соберете новый атрибут во время регистрации в Azure Active Directory B2C (Azure AD B2C). Вы получите город пользователя, настроите его в виде раскрывающегося списка и определите, требуется ли предоставлять его.

Начальные данные пользователей можно собирать с помощью пути взаимодействия пользователя при регистрации или входа. Дополнительные утверждения можно собрать позже при редактировании профиля. Когда Azure AD B2C собирает информацию непосредственно из пользователя в интерактивном режиме, инфраструктура процедур идентификации использует его [самостоятельно утвержденный технический профиль](self-asserted-technical-profile.md). В этом примере вы выполните следующие действия:

1. Определите утверждение "город".
1. Попросите пользователя указать город.
1. Сохранить город в профиле пользователя в каталоге Azure AD B2C.
1. Прочитайте утверждение City из Azure AD B2C каталога при каждом входе.
1. Верните город в приложение проверяющей стороны после входа или регистрации.  

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). У вас должна быть Рабочая настраиваемая политика для регистрации и входа с помощью социальных сетей и локальных учетных записей.

## <a name="define-a-claim"></a>Определение утверждения

Утверждение предоставляет временное хранилище данных во время выполнения политики Azure AD B2C. [Схема утверждений](claimsschema.md) — это место, где объявляются утверждения. Для определения утверждения используются такие элементы:

- **DisplayName** — это строка, определяющая пользовательскую метку.
- [DataType](claimsschema.md#datatype) — тип утверждения.
- **UserHelpText** — позволяет пользователю узнать требования.
- [Усеринпуттипе](claimsschema.md#userinputtype) — тип элемента управления вводом, например текстовое поле, выбор переключателя, раскрывающийся список или множественный выбор.

Откройте файл расширений политики. Например, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.

1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Нахождение элемента [ClaimsSchema](claimsschema.md) . Если такой элемент не существует, добавьте его.
1. Добавьте утверждение City в элемент **ClaimsSchema** .  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Добавление утверждения в пользовательский интерфейс

Следующие технические профили [автоматически](self-asserted-technical-profile.md)вызываются, когда пользователь должен предоставить входные данные:

- **Локалаккаунтсигнупвислогонемаил** — поток регистрации локальной учетной записи.
- **SelfAsserted-социальное** -Федеративная учетная запись Вход пользователя в систему в первый раз.
- **SelfAsserted-ProfileUpdate** — изменение потока профиля.

Чтобы получить утверждение "город" во время регистрации, его необходимо добавить в качестве выходного утверждения в `LocalAccountSignUpWithLogonEmail` техническом профиле. Переопределите этот технический профиль в файле расширения. Укажите полный список исходящих утверждений для управления порядком, в котором утверждения представлены на экране. Найдите элемент **ClaimsProviders**. Добавьте новый Клаимспровидерс следующим образом:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Чтобы получить утверждение "город" после первоначального входа с помощью федеративной учетной записи, его необходимо добавить в качестве выходного утверждения в `SelfAsserted-Social` техническом профиле. Чтобы пользователи локальных и федеративных учетных записей могли изменять данные профиля позже, добавьте выходное утверждение в `SelfAsserted-ProfileUpdate` технический профиль. Переопределите эти технические профили в файле расширения. Укажите весь список исходящих утверждений для управления порядком представления заявок на экране. Найдите элемент **ClaimsProviders**. Добавьте новый Клаимспровидерс следующим образом:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Чтение и запись утверждения

Следующие технические профили являются [Active Directory техническими профилями](active-directory-technical-profile.md), которые считывают и записывают данные в Azure Active Directory.  
Используйте `PersistedClaims` для записи данных в профиль пользователя и `OutputClaims` для чтения данных из профиля пользователя в соответствующих технических профилях Active Directory.

Переопределите эти технические профили в файле расширения. Найдите элемент **ClaimsProviders**.  Добавьте новый Клаимспровидерс следующим образом:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Включить утверждение в маркер 

Чтобы вернуть заявку City к приложению проверяющей стороны, добавьте выходное утверждение в файл <em>`SocialAndLocalAccounts/` **`SignUpOrSignIn.xml`** </em>. Выходное утверждение будет добавлено в маркер после успешного пути взаимодействия пользователя и будет отправлено в приложение. Измените элемент технического профиля в разделе проверяющей стороны, чтобы добавить город в качестве выходного утверждения.
 
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
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий ваш клиент Azure AD.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Инфраструктура процедур идентификации**.
5. Выберите **Отправка пользовательской политики** и отправьте два файла политики, которые вы изменили ранее.
2. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
3. Вы сможете зарегистрироваться, используя адрес электронной почты.

Экран регистрации должен выглядеть как на следующем снимке экрана:

![Снимок экрана с измененным вариантом регистрации](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Токен, отправленный обратно в ваше приложение, включает утверждение `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об элементе [ClaimsSchema](claimsschema.md) см. в справочнике по инфраструктура процедур идентификации.
- Узнайте, как [использовать настраиваемые атрибуты в пользовательской политике изменения профиля](custom-policy-custom-attributes.md).
