---
title: Настройка Azure AD B2C в качестве IdPа SAML для приложений
title-suffix: Azure AD B2C
description: Настройка Azure AD B2C для предоставления приложениям утверждений протокола SAML (поставщики служб). Azure AD B2C будет использоваться в качестве единого поставщика удостоверений (IdP) для приложения SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 38c98a65ac0b0f95a9a6e111a79b5dede04912c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229754"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Регистрация приложения SAML в Azure AD B2C

Из этой статьи вы узнаете, как настроить Azure Active Directory B2C (Azure AD B2C) в качестве поставщика удостоверений язык разметки зявлений системы безопасности (SAML) (SAML) (IdP) для приложений.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Обзор сценария

Организации, использующие Azure AD B2C в качестве решения для управления удостоверениями и доступом клиентов, могут потребовать взаимодействия с поставщиками удостоверений или приложениями, настроенными для проверки подлинности с помощью протокола SAML.

Azure AD B2C достигает взаимодействия SAML одним из двух способов:

* Выступая в качестве *поставщика удостоверений* (IDP) и достигая единого входа (SSO) с поставщиками служб на основе SAML (приложения).
* Выполняя роль *поставщика услуг* (SP) и взаимодействующего с поставщиками удостоверений на основе SAML, такими как SALESFORCE и ADFS.

![Схема с B2C в качестве поставщика удостоверений слева и B2C в качестве поставщика услуг справа](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Суммирование двух неисключительных основных сценариев с помощью SAML:

| Сценарий | Роль Azure AD B2C | Практическое руководство |
| -------- | ----------------- | ------- |
| Мое приложение предполагает утверждение SAML для завершения проверки подлинности. | **Azure AD B2C выступает в качестве поставщика удостоверений (IdP)**<br />Azure AD B2C выступает в качестве IdPа SAML для приложений. | Эта статья |
| Моим пользователям требуется единый вход с помощью соответствующего SAML поставщика удостоверений, такого как ADFS, Salesforce или Shibboleth.  | **Azure AD B2C выступает в качестве поставщика услуг (SP)**<br />Azure AD B2C выступает в качестве поставщика услуг при подключении к поставщику удостоверений SAML. Это прокси-сервер федерации между приложением и поставщиком удостоверений SAML.  | <ul><li>[Настройка входа с помощью ADFS в качестве IdP SAML с помощью настраиваемых политик](identity-provider-adfs2016-custom.md)</li><li>[Настройка входа с помощью поставщика SAML SalesForce с помощью настраиваемых политик](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Предварительные условия

* Выполните действия, описанные в разделе [Приступая к работе с пользовательскими политиками в Azure AD B2C](custom-policy-get-started.md). Вам потребуется настраиваемая политика *SocialAndLocalAccounts* из начального пакета настраиваемой политики, описанного в этой статье.
* Основные сведения о протоколе язык разметки зявлений системы безопасности (SAML) (SAML).
* Веб-приложение, настроенное как поставщик службы SAML (SP). В этом руководстве вы можете использовать [тестовое приложение SAML][samltest] , которое мы предоставляем.

## <a name="components-of-the-solution"></a>Компоненты решения

Для этого сценария требуется три основных компонента:

* **Поставщик службы** SAML с возможностью отправки запросов SAML, а также получения, декодирования и реагирования на утверждения saml из Azure AD B2C. Это также называется проверяющей стороной.
* Общедоступная **Конечная точка МЕТАДАННЫХ** SAML для поставщика услуг.
* [клиент Azure AD B2C](tutorial-create-tenant.md);

Если у вас еще нет поставщика служб SAML и связанной конечной точки метаданных, можно использовать этот пример приложения SAML, который мы сделали доступным для тестирования:

[Тестовое приложение SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Настройка сертификатов

Чтобы создать отношение доверия между поставщиком услуг и Azure AD B2C, необходимо предоставить сертификаты X509 веб-приложения.

* **Сертификаты поставщика услуг**
  * Сертификат с закрытым ключом, хранящимся в вашем веб-приложении. Этот сертификат используется поставщиком услуг для подписывания запроса SAML, отправленного в Azure AD B2C. Azure AD B2C считывает открытый ключ из метаданных поставщика услуг для проверки подписи.
  * Используемых Сертификат с закрытым ключом, хранящимся в вашем веб-приложении. Azure AD B2C считывает открытый ключ из метаданных поставщика услуг для шифрования утверждения SAML. Затем поставщик услуг использует закрытый ключ для расшифровки утверждения.
* **Сертификаты Azure AD B2C**
  * Сертификат с закрытым ключом в Azure AD B2C. Этот сертификат используется Azure AD B2C для подписания ответа SAML, отправленного поставщику услуг. Поставщик услуг считывает открытый ключ метаданных Azure AD B2C для проверки подписи ответа SAML.

Можно использовать сертификат, выданный общедоступным центром сертификации, или для этого руководства — самозаверяющий сертификат.

### <a name="11-prepare-a-self-signed-certificate"></a>1,1. Подготовка самозаверяющего сертификата

Если у вас еще нет сертификата, для работы с этим руководством можно использовать самозаверяющий сертификат. В Windows для создания сертификата можно использовать командлет [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) PowerShell.

1. Выполните эту команду PowerShell, чтобы создать самозаверяющий сертификат. Измените `-Subject` аргумент соответствующим образом для приложения и Azure AD B2C имя клиента. Можно также настроить `-NotAfter` дату для указания другого срока действия сертификата.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Открыть **Управление сертификатами** > пользователей**Личные** > **Сертификаты** > **пользователя** > *yourappname.yourtenant.onmicrosoft.com*
1. Выберите сертификат > **действие** > **Экспорт** **всех задач** > 
1. Нажмите кнопку **Да** > **Далее** > **Да, экспортировать закрытый ключ** > **Далее** .
1. Принять значения по умолчанию для **формата экспортируемого файла**
1. Укажите пароль для сертификата

### <a name="12-upload-the-certificate"></a>1,2. Отправка сертификата

Затем отправьте утверждение SAML и сертификат подписи ответа в Azure AD B2C.

1. Войдите в [портал Azure](https://portal.azure.com) и перейдите к своему клиенту Azure AD B2C.
1. В разделе **политики**выберите **инфраструктура процедур идентификации** , а затем **ключи политики**.
1. Нажмите кнопку **Добавить**, а затем выберите **Параметры** > **Отправить**.
1. Введите **имя**, например *самлидпцерт*. Префикс *B2C_1A_* будет автоматически добавлен к имени ключа.
1. Отправьте сертификат с помощью элемента управления "передача файла".
1. Введите пароль сертификата.
1. Нажмите кнопку **создания**.
1. Убедитесь, что ключ отображается ожидаемым образом. Например, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Подготовка политики

### <a name="21-create-the-saml-token-issuer"></a>2,1. Создание издателя токена SAML

Теперь добавьте возможность клиента, чтобы выдать токены SAML, используя технические профили [издателя токена SAML](saml-issuer-technical-profile.md) и [поставщика сеанса SAML](custom-policy-reference-sso.md#samlssosessionprovider) .

Откройте `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** в начальном пакете настраиваемой политики.

Откройте `<ClaimsProviders>` раздел и добавьте следующий фрагмент XML-кода.

Значение `IssuerUri` метаданных можно изменить. Это URI издателя, который возвращается в ответе SAML от Azure AD B2C. Приложение проверяющей стороны должно быть настроено на принятие URI издателя во время проверки утверждения SAML.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. добавьте политику проверяющей стороны SAML.

Теперь, когда клиент может выдавать утверждения SAML, необходимо создать политику проверяющей стороны SAML и изменить путь взаимодействия пользователя, чтобы он выдавал утверждение SAML вместо JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1. Создание политики регистрации или входа

1. Создайте копию файла *SignUpOrSignin. XML* в рабочем каталоге Starter Pack и сохраните ее с новым именем. Например, *сигнупорсигнинсамл. XML*. Это файл политики проверяющей стороны.

1. Откройте файл *сигнупорсигнинсамл. XML* в предпочитаемом редакторе.

1. Измените `PolicyId` и `PublicPolicyUri` для политики на _B2C_1A_signup_signin_saml_ и `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` , как показано ниже.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Добавьте следующий фрагмент кода XML непосредственно перед `<RelyingParty>` элементом. Этот код XML перезаписывает шаг 7 в пути взаимодействия пользователя _SignUpOrSignIn_ . Если вы `order` начали из другой папки в начальном пакете или настроили путь взаимодействия пользователя, добавив или удалив шаги оркестрации, убедитесь, что число (в элементе) соответствует указанному в пути пользователя для шага издателя маркера (например, в других папках начального пакета это шаг 4 для `LocalAccounts`, 6 для `SocialAccounts` и 9 для `SocialAndLocalAccountsWithMfa`).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Замените весь `<TechnicalProfile>` элемент в `<RelyingParty>` элементе следующим техническим XML-документом профиля.

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Обновите `tenant-name` имя клиента Azure AD B2C.

Окончательный файл политики проверяющей стороны должен выглядеть следующим образом:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2. Отправка и тестирование метаданных политики

Сохраните изменения и отправьте новый файл политики. После отправки обеих политик (расширение и файлы проверяющей стороны) Откройте веб-браузер и перейдите к метаданным политики.

Azure AD B2C политика IDP метаданные — сведения, используемые в протоколе SAML для предоставления конфигурации поставщика удостоверений SAML. Метаданные определяют расположение служб, например вход и выход, сертификаты, метод входа и многое другое. Метаданные политики Azure AD B2C доступны по следующему URL-адресу. Замените `tenant-name` именем клиента Azure AD B2C и `policy-name` именем (ID) политики:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Теперь ваша настраиваемая политика и клиент Azure AD B2C готовы. Затем создайте регистрацию приложения в Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Установка приложения в каталоге Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4,1. Регистрация приложения в Azure Active Directory

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Щелкните **Регистрация приложений (предварительная версия)** и выберите **Новая регистрация**.
1. Введите **имя** приложения. Например, *SAMLApp1*.
1. В разделе **Поддерживаемые типы учетных записей**выберите **учетные записи только в этом каталоге Организации** .
1. В разделе **URI перенаправления**выберите **веб**, а затем `https://localhost`введите. Это значение можно изменить позже в манифесте регистрации приложения.
1. Выберите **Зарегистрировать**.

### <a name="42-update-the-app-manifest"></a>4,2 обновление манифеста приложения

Для приложений SAML необходимо настроить несколько свойств в манифесте регистрации приложения.

1. В [портал Azure](https://portal.azure.com)перейдите к регистрации приложения, созданному в предыдущем разделе.
1. В разделе **Управление**выберите **Манифест** , чтобы открыть редактор манифеста. Вы изменяете несколько свойств в следующих разделах.

#### <a name="identifieruris"></a>identifierUris

`identifierUris` — Это коллекция строк, содержащая определяемые пользователем универсальные коды ресурсов (URI), которые уникально идентифицируют веб-приложение в своем клиенте Azure AD B2C. Поставщик услуг должен задать это значение в `Issuer` ЭЛЕМЕНТЕ запроса SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Это свойство представляет общедоступный URL-адрес метаданных поставщика услуг. URL-адрес метаданных может указывать на файл метаданных, передаваемый в любую анонимно доступную конечную точку, например хранилище больших двоичных объектов.

Метаданные — это сведения, используемые в протоколе SAML для предоставления конфигурации субъекта SAML, например поставщика услуг. Метаданные определяют расположение таких служб, как вход и выход, сертификаты, метод входа и многое другое. Azure AD B2C считывает метаданные поставщика услуг и действует соответствующим образом. Метаданные не требуются. Можно также указать некоторые атрибуты, такие как URI ответа и URI выхода, непосредственно в манифесте приложения.

Если есть свойства, указанные как в URL-адресе метаданных SAML, *так* и в манифесте регистрации приложения, они **объединяются**. Свойства, указанные в URL-адресе метаданных, обрабатываются первыми и имеют приоритет.

Для работы с этим руководством, в котором используется тестовое приложение SAML, используйте следующее `samlMetadataUrl`значение для:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>Реплюрлсвистипе (необязательно)

Если не указать URI метаданных, можно явно указать URL-адрес ответа. Это необязательное свойство `AssertionConsumerServiceUrl` представляет`SingleSignOnService` (URL-адрес в метаданных поставщика услуг) `BindingType` , а предполагается `HTTP POST`, что имеет значение.

Если вы решили настроить URL-адрес ответа и URL-адрес выхода в манифесте приложения без использования метаданных поставщика услуг, Azure AD B2C не будет проверять подпись запроса SAML или не шифровать ответ SAML.

Для работы с этим руководством, в котором используется тестовое приложение SAML, задайте `url` свойству `replyUrlsWithType` значение, показанное в следующем фрагменте кода JSON.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>Логаутурл (необязательно)

Это необязательное свойство `Logout` представляет URL`SingleLogoutService` -адрес (URL-адрес в метаданных проверяющей стороны `BindingType` ), а для этого — `Http-Redirect`значение.

Для работы с этим руководством, в котором используется тестовое приложение `logoutUrl` SAML, `https://samltestapp2.azurewebsites.net/logout`оставьте значение:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Обновление кода приложения

Последним шагом является включение Azure AD B2C в качестве SAML IdP в приложении проверяющей стороны SAML. Каждое приложение отличается друг от друга и действия, которые необходимо выполнить. Дополнительные сведения см. в документации приложения.

Обычно требуются некоторые или все перечисленные ниже действия.

* **Метаданные**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Издатель**: использование entityID в файле метаданных
* **URL-адрес входа/конечная точка SAML/URL-адрес SAML**: проверьте значение в файле метаданных.
* **Сертификат**. это *B2C_1A_SamlIdpCert*, но без закрытого ключа. Чтобы получить открытый ключ сертификата, сделайте следующее:

    1. Перейдите по URL-адресу метаданных, указанному выше.
    1. Скопируйте значение в `<X509Certificate>` элемент.
    1. Вставьте его в текстовый файл.
    1. Сохраните текстовый файл как *CER* -файл.

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1. тестирование с помощью тестового приложения SAML (необязательно)

Для работы с этим руководством с помощью нашего [тестового приложения SAML][samltest]:

* Обновление имени клиента
* Обновите имя политики, например *B2C_1A_signup_signin_saml*
* Укажите URI издателя:`https://contoso.onmicrosoft.com/app-name`

Выберите **имя входа** , и вы увидите экран входа пользователя. После входа в пример приложения возвращается утверждение SAML.

## <a name="sample-policy"></a>Пример политики

Мы предоставляем полный пример политики, который можно использовать для тестирования с помощью тестового приложения SAML.

1. Скачайте [Пример политики для входа с помощью SAML-SP-инициируемые](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Обновите `TenantId` в соответствии с именем клиента, например *contoso.b2clogin.com*
1. Сохраняет имя политики *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Поддерживаемые и неподдерживаемые SAML модальностей

Следующие сценарии проверяющей стороны SAML (RP) поддерживаются с помощью собственной конечной точки метаданных:

* Несколько URL-адресов выхода или после привязки для URL-адреса выхода в объекте приложения или участника службы.
* Укажите ключ подписывания, чтобы проверить запросы RP в объекте приложения или субъекта-службы.
* Укажите ключ шифрования маркера в объекте приложения или субъекта-службы.
* Имена входа, инициированные поставщиком удостоверений, в настоящее время не поддерживаются в предварительной версии.

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [протоколе SAML можно найти на веб-сайте Oasis](https://www.oasis-open.org/).
- Получите тестовое веб-приложение SAML из [репозитория сообщества GitHub Azure AD B2C](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
