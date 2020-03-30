---
title: Добавление утверждений и настройка пользовательских ввода в пользовательских политиках
titleSuffix: Azure AD B2C
description: Узнайте, как настраивать пользовательский ввод и использовать утверждения при регистрации или входе в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473682"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Добавление утверждений и настройка пользовательского ввода с помощью настраиваемых политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье вы собираете новый атрибут во время поездки за регистрацию в Azure Active Directory B2C (Azure AD B2C). Вы получите город пользователей, наверснете его как выпадение и определите, требуется ли это предоставляться.

> [!NOTE]
> В этом примере используется встроенная претензия "город". Вместо этого можно выбрать один из [поддерживаемых атрибутов Azure AD B2C](user-profile-attributes.md) или пользовательский атрибут. Чтобы использовать пользовательский атрибут, [включите пользовательские атрибуты в вашей политике.](custom-policy-custom-attributes.md) Чтобы использовать другой встроенный или пользовательский атрибут, замените «город» атрибутом по вашему выбору, например встроенным атрибутом *jobTitle* или пользовательским атрибутом, *например extension_loyaltyId.*  

Вы можете собирать исходные данные от пользователей, используя регистрацию или входе в путешествие пользователя. Дополнительные утверждения можно собрать позже при редактировании профиля. В любое время, когда Azure AD B2C собирает информацию непосредственно от пользователя в интерактивном режиме, система Identity Experience использует свой [самоутверждаемый технический профиль.](self-asserted-technical-profile.md) В этом примере вы:

1. Определите претензию "города". 
1. Спросите пользователя о своем городе.
1. Упорный город в профиль пользователя в каталоге Azure AD B2C.
1. Ознакомьте с претензией города из каталога Azure AD B2C на каждом входе.
1. Верните город в заявку на участие в приеме после регистрации или регистрации.  

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Вы должны иметь рабочую таможенную политику для регистрации и регистрации с социальными и местными учетными записями.

## <a name="define-a-claim"></a>Определить претензию

Претензия предусматривает временное хранение данных во время выполнения политики Azure AD B2C. [Схема претензий](claimsschema.md) — это место, где вы объявляете свои претензии. Для определения утверждения используются такие элементы:

- **DisplayName** — это строка, определяющая пользовательскую метку.
- [DataType](claimsschema.md#datatype) - Тип претензии.
- **UserHelpText** — позволяет пользователю узнать требования.
- [UserInputType](claimsschema.md#userinputtype) - Тип элемента управления входной связью, такой как текстовый ящик, выбор радио, список выпадающих или несколько вариантов.

Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.

1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Найдите элемент [ClaimsSchema.](claimsschema.md) Если такой элемент не существует, добавьте его.
1. Добавьте претензию города к элементу **ClaimsSchema.**  

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

## <a name="add-a-claim-to-the-user-interface"></a>Добавление претензии к пользовательскому интерфейсу

Следующие технические профили [самоутверждаются,](self-asserted-technical-profile.md)ссылаются, когда пользователь должен предоставить входные данные:

- **LocalAccountSignUpWithLogonEmail** - Локальный поток регистрации учетной записи.
- **SelfAsserted-Social** - Федеративная учетная запись, впервые всданный пользователем.
- **SelfAsserted-ProfileUpdate** - Отсражайте поток профиля.

Для получения претензии города во время регистрации она должна `LocalAccountSignUpWithLogonEmail` быть добавлена в качестве претензии к техническому профилю. Переопределить этот технический профиль в файле расширения. Укажите весь список выходных требований для контроля за заказом, на экране представлены претензии. Найдите элемент **ClaimsProviders**. Добавить новые поставщики претензий следующим образом:

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

Чтобы получить претензию города после первоначального входного в систему с федеративной учетной записью, она должна быть добавлена в качестве претензии к техническому `SelfAsserted-Social` профилю. Для того, чтобы местные пользователи и пользователи учетных записей могли `SelfAsserted-ProfileUpdate` позже отсеить данные профиля, добавьте претензию вывода в технический профиль. Переопределить эти технические профили в файле расширения. Укажите весь список выходных требований для контроля за заказом, на экране представлены претензии. Найдите элемент **ClaimsProviders**. Добавить новые поставщики претензий следующим образом:

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

## <a name="read-and-write-a-claim"></a>Читать и писать претензии

Следующими техническими профилями являются [технические профили Active Directory,](active-directory-technical-profile.md)которые считывают и записывают данные в Active Directory Azure.  
Используется `PersistedClaims` для записи данных `OutputClaims` в профиль пользователя и чтения данных из профиля пользователя в соответствующих технических профилях Active Directory.

Переопределить эти технические профили в файле расширения. Найдите элемент **ClaimsProviders**.  Добавить новые поставщики претензий следующим образом:

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

## <a name="include-a-claim-in-the-token"></a>Включить претензию в токен 

Чтобы вернуть претензию города обратно в заявку на участие <em> `SocialAndLocalAccounts/` </em> в заявке, добавьте претензию вывода в файл. Претензия на вывод будет добавлена в токен после успешного путешествия пользователя и будет отправлена в приложение. Измените элемент технического профиля в разделе опирающейся стороны, чтобы добавить город в качестве требования вывода.
 
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
2. Убедитесь, что вы используете каталог, содержащий ваш клиент Azure AD, выбрав фильтр **подписки каталога в** верхнем меню и выбрав каталог, содержащий арендатор Azure AD.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Инфраструктура процедур идентификации**.
5. Выберите **Отправка пользовательской политики** и отправьте два файла политики, которые вы изменили ранее.
2. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
3. Вы сможете зарегистрироваться, используя адрес электронной почты.

Экран регистрации должен выглядеть так же, как следующий скриншот:

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

- Подробнее об элементе [ClaimsSchema](claimsschema.md) читайте в справке IEF.
- Узнайте, как [использовать пользовательские атрибуты в политике изменения пользовательского профиля.](custom-policy-custom-attributes.md)
