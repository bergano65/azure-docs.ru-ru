---
title: Накончайте Azure AD B2C в качестве SAML IdP для ваших приложений
title-suffix: Azure AD B2C
description: Как настроить Azure AD B2C для предоставления утверждений протоколов SAML вашим приложениям (поставщикам услуг). Azure AD B2C будет выступать в качестве единого поставщика идентификационных данных (IdP) для вашего приложения SAML.
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
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051623"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Регистрация приложения SAML в Azure AD B2C

В этой статье вы узнаете, как настроить B2C Active Directory (Azure AD B2C) для того, чтобы выступать в качестве поставщика идентификационных данных (IDP) для идентификации языка защиты данных (SAML).

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Обзор сценария

Организациям, использующим Azure AD B2C в качестве решения для идентификации клиентов и управления доступом, может потребоваться взаимодействие с поставщиками идентификационных данных или приложениями, настроенными на аутентификации с помощью протокола SAML.

Azure AD B2C обеспечивает совместимость SAML одним из двух способов:

* Действуя в качестве *поставщика идентификационных* данных (IdP) и достигая единого знака (SSO) с поставщиками услуг на основе SAML (ваши приложения)
* Действуя в качестве *поставщика услуг* (SP) и взаимодействуя с поставщиками идентификационных данных на основе SAML, такими как Salesforce и ADFS

![Диаграмма с B2C в качестве поставщика идентификационных данных слева и B2C в качестве поставщика услуг справа](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Резюме двух неэксклюзивных основных сценариев с SAML:

| Сценарий | Роль Azure AD B2C | Практические советы |
| -------- | ----------------- | ------- |
| Мое приложение ожидает, что утверждение SAML завершит проверку подлинности. | **Azure AD B2C выступает в качестве поставщика идентификационных данных (IdP)**<br />Azure AD B2C выступает в качестве SAML IdP для приложений. | Эта статья |
| Моим пользователям нужен один подписок с поставщиком идентификационных данных, совместимым с SAML, таким как ADFS, Salesforce или Shibboleth.  | **Azure AD B2C выступает в качестве поставщика услуг (SP)**<br />Azure AD B2C выступает в качестве поставщика услуг при подключении к поставщику идентификационных данных SAML. Это доверенное лицо федерации между вашим приложением и поставщиком идентификационных данных SAML.  | <ul><li>[Настройка регистрации в ADFS в качестве SAML IdP с помощью пользовательских политик](identity-provider-adfs2016-custom.md)</li><li>[Настройка регистрации с поставщиком Salesforce SAML с использованием пользовательских политик](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Предварительные требования

* Завершите шаги в [Get started с пользовательскими политиками в Azure AD B2C.](custom-policy-get-started.md) Вам нужна пользовательская политика *SocialAndLocalAccounts* из пакета пользовательских программ, обсуждаемых в статье.
* Базовое понимание протокола Language Markup Language (SAML) по вопросам безопасности.
* Веб-приложение, настроенное в качестве поставщика услуг SAML (SP). Для этого учебника вы можете использовать тестируемое [приложение SAML,][samltest] которое мы предоставляем.

## <a name="components-of-the-solution"></a>Компоненты решения

Для этого сценария требуется три основных компонента:

* Поставщик **услуг** SAML, который может отправлять запросы SAML, получать, декодировать и отвечать на утверждения SAML от Azure AD B2C. Это также известно как relying сторона.
* Общедоступная **конечная точка метаданных** SAML для вашего поставщика услуг.
* [Клиент Azure AD B2C](tutorial-create-tenant.md)

Если у вас еще нет поставщика услуг SAML и связанной с ними конечная точка метаданных, вы можете использовать этот пример приложения SAML, которое мы сделали доступным для тестирования:

[Тест-приложение SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Настройка сертификатов

Чтобы построить доверительные отношения между поставщиком услуг и Azure AD B2C, необходимо предоставить сертификаты X509 в веб-приложении.

* **Сертификаты поставщика услуг**
  * Сертификат с закрытым ключом, хранящимся в веб-приложении. Этот сертификат используется вашим поставщиком услуг для подписания запроса SAML, отправленного в Azure AD B2C. Azure AD B2C считывает общедоступный ключ от метаданных поставщика услуг для проверки подписи.
  * (Необязательно) Сертификат с закрытым ключом, хранящимся в веб-приложении. Azure AD B2C считывает общедоступный ключ от метаданных поставщика услуг для шифрования утверждения SAML. Затем поставщик услуг использует закрытый ключ для расшифровки утверждения.
* **Сертификаты Azure AD B2C**
  * Сертификат с закрытым ключом в Azure AD B2C. Этот сертификат используется Azure AD B2C для подписания ответа SAML, отправленного поставщику услуг. Поставщик услуг читает общедоступный ключ метаданных Azure AD B2C для проверки подписи ответа SAML.

Вы можете использовать сертификат, выданный государственным органом сертификата, или, для этого учебника, самоподписанный сертификат.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Подготовка сертификата, подписанного самостоятельно

Если у вас еще нет сертификата, вы можете использовать сертификат, подписанный самостоятельно, для этого учебника. В Windows для создания сертификата можно использовать смдлет PowerShell [New-SelfSignedCertificate.](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)

1. Выполните эту команду PowerShell для создания сертификата, подписанного самостоятельно. Измените `-Subject` аргумент, подходящий для приложения и имени клиента Azure AD B2C. Можно также настроить `-NotAfter` дату, чтобы указать другой срок действия сертификата.

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

1. Открытое **управление пользовательскими сертификатами** > **Текущие** > **личные** > **сертификаты** > пользователя*yourappname.yourtenant.onmicrosoft.com*
1. Выберите сертификат >**Export** **действии** > Экспорт всех**задач** > 
1. Выберите **Да** > **Далее** > **Да, экспортировать частный ключ** > **Далее**
1. Принять по умолчанию для **формата экспортных файлов**
1. Предоставить пароль для сертификата

### <a name="12-upload-the-certificate"></a>1.2 Загрузить сертификат

Затем загрузите сертификат подписи SAML на Azure AD B2C.

1. Вопийте на [портале Azure](https://portal.azure.com) и просматривайте своего клиента Azure AD B2C.
1. В соответствии с **политиками**выберите **рамки опыта идентификации,** а затем **ключи политики.**
1. Выберите **Добавить,** а затем выберите **Параметры** > **Загрузка**.
1. Введите **имя,** например *SamlIdpCert*. Префикс *B2C_1A_* будет автоматически добавлен к имени ключа.
1. Загрузите сертификат с помощью управления файлами загрузки.
1. Введите пароль сертификата.
1. Выберите **Создать**.
1. Убедитесь, что ключ отображается как ожидалось. Например, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Подготовьте свою политику

### <a name="21-create-the-saml-token-issuer"></a>2.1 Создание эмитента токенов SAML

Теперь добавьте возможность для вашего клиента выдавать токены SAML, используя технические профили [эмитента токенов SAML](saml-issuer-technical-profile.md) и поставщика сеансов [SAML.](custom-policy-reference-sso.md#samlssosessionprovider)

Открыть `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** в пользовательском пакете стартера политики.

Найдите `<ClaimsProviders>` раздел и добавьте следующий фрагмент XML.

Можно изменить значение `IssuerUri` метаданных. Это эмитент URI, который возвращается в ответ SAML от Azure AD B2C. Ваше приложение, полагающееся на сторону, должно быть настроено для приема эмитента URI во время проверки утверждения SAML.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Добавьте политику samL, опираясь на политику партии

Теперь, когда ваш арендатор может выдать утверждения SAML, вам необходимо создать политику SAML, полагающейся на сторону, и изменить путешествие пользователя так, чтобы он выдал утверждение SAML вместо JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Создание политики регистрации или регистрации

1. Создайте копию файла *SignUpOrSignin.xml* в рабочем каталоге стартового пакета и сохраните его с новым именем. Например, *SignUpOrSigninSAML.xml*. Это ваш файл политики полагаться на партию.

1. Откройте файл *SignUpOrSigninSAML.xml* в предпочитаемом редакторе.

1. Изменить `PolicyId` и `PublicPolicyUri` политики, чтобы `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` _B2C_1A_signup_signin_saml_ и, как показано ниже.

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

1. Добавьте следующий фрагмент XML `<RelyingParty>` непосредственно перед элементом. Это XML перезаписывает оркестровку шаг номер 7 путешествия пользователя _SignUpOrSignIn._ Если вы начали с другой папки в стартовом пакете или настроили путешествие пользователя, добавив `order` или удалив шаги оркестровки, убедитесь, что номер (в элементе) выравнивается с номером, `LocalAccounts`указанным `SocialAccounts` в пользовательском путешествии для шага эмитента токенов (например, в других папках стартового пакета это шаг номер 4 для, 6 для и 9 для). `SocialAndLocalAccountsWithMfa`

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Замените `<TechnicalProfile>` весь элемент `<RelyingParty>` элемента следующим техническим профилем XML.

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

1. Обновление `tenant-name` с именем вашего клиента Azure AD B2C.

Ваш последний файл политики полагаться на партию должен выглядеть следующим образом:

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Загрузить и протестировать метаданные политики

Сохраните изменения и загрузите новый файл политики. После загрузки обеих политик (расширение и файлы опорной стороны) откройте веб-браузер и перейдите к метаданным политики.

Метаданные IDP-политики политики Azure AD B2C — это информация, используемая в протоколе SAML для выявления конфигурации поставщика идентификационных данных SAML. Метаданные определяют расположение служб, например вход и выход, сертификаты, метод входа и многое другое. Метаданные политики Azure AD B2C доступны по следующему URL- Замените `tenant-name` имя вашего клиента Azure AD `policy-name` B2C и имя (ID) политики:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Ваша пользовательская политика и арендатор Azure AD B2C готовы. Затем создайте регистрацию приложения в Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Настройка приложения в каталоге Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Зарегистрируйте свое заявление в Active Directory Azure

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Щелкните **Регистрация приложений (предварительная версия)** и выберите **Новая регистрация**.
1. Введите **имя** приложения. Например, *SAMLApp1*.
1. Под **типами поддерживаемых учетных записей**выберите **учетные записи только в этом каталоге организации**
1. Под **перенаправлениеURI**, выберите `https://localhost` **веб**, а затем введите . Это значение можно изменить позже в манифесте регистрации приложения.
1. Выберите **Зарегистрировать**.

### <a name="42-update-the-app-manifest"></a>4.2 Обновление манифеста приложения

Для приложений SAML есть несколько свойств, которые необходимо настроить в манифесте регистрации приложения.

1. На [портале Azure](https://portal.azure.com)перейдите к регистрации приложения, созданной в предыдущем разделе.
1. Под **управлением,** выберите **манифест,** чтобы открыть манифест редактора. Вы изменяете несколько свойств в следующих разделах.

#### <a name="identifieruris"></a>identifierUris

Представляет `identifierUris` собой строку, содержащую uri(ы), которая однозначно идентифицирует веб-приложение в рамках его арендатора Azure AD B2C. Поставщик услуг должен установить `Issuer` это значение в элементе запроса SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Это свойство представляет общедоступный URL-адрес метаданных поставщика услуг. URL-адрес метаданных может указывать на файл метаданных, загруженный в любую анонимно доступную конечную точку, например хранилище капли.

Метаданные — это информация, используемая в протоколе SAML для выявления конфигурации участника SAML, например поставщика услуг. Метаданные определяют местоположение служб, таких как входе и входе, сертификаты, метод входе и многое другое. Azure AD B2C считывает метаданные поставщика услуг и действует соответствующим образом. Метаданные не требуются. Вы также можете указать некоторые атрибуты, такие как ответ URI и выход URI непосредственно в манифесте приложения.

Если есть свойства, указанные *как* в URL-адресе метаданных SAML, так и в манифесте регистрации приложения, они **объединяются.** Свойства, указанные в URL метаданных, обрабатываются первыми и имеют приоритет.

Для этого учебника, в котором используется тестируемое приложение SAML, используйте следующее значение для: `samlMetadataUrl`

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (необязательно)

Если вы не предоставляете метаданные URI, вы можете прямо указать URL-адрес ответа. Это дополнительное `AssertionConsumerServiceUrl` свойство представляет (URL`SingleSignOnService` в метаданных поставщика `HTTP POST`услуг) и `BindingType` предполагается, что это .

Если вы решите настроить URL-адрес ответа и URL-адрес выхода в манифесте приложения без использования метаданных поставщика услуг, Azure AD B2C не будет проверять подпись запроса SAML и не шифрует ответ SAML.

Для этого учебника, в котором используется тестируемое приложение SAML, установите `url` свойство значения, указанного `replyUrlsWithType` в следующем фрагменте JSON.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (необязательно)

Это дополнительное `Logout` свойство`SingleLogoutService` представляет URL (URL в метаданных предполагаемой стороны), и `BindingType` для этого `Http-Redirect`предполагается, что это .

Для этого учебника, который использует приложение `logoutUrl` для `https://samltestapp2.azurewebsites.net/logout`тестирования SAML, оставьте набор:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Обновление кода приложения

Последним шагом является включение Azure AD B2C в качестве SAML IdP в ваше приложение samL, полагающееся на сторону. Каждое приложение отличается, и шаги для этого различаются. Проконсультируйтесь с документацией приложения для получения подробной информации.

Некоторые или все следующие, как правило, требуется:

* **Метаданные**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Эмитент**: Используйте сущность ID в файле метаданных
* **Логин Url/SAML конечная точка / SAML Url**: Проверить значение в файле метаданных
* **Сертификат**: Это *B2C_1A_SamlIdpCert,* но без частного ключа. Чтобы получить общедоступный ключ сертификата:

    1. Перейдите на URL-адрес метаданных, указанный выше.
    1. Копирование значения `<X509Certificate>` в элементе.
    1. Вставьте его в текстовый файл.
    1. Сохранить текстовый файл в виде *файла .cer.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Тест с приложением SAML Test (необязательно)

Чтобы завершить этот учебник с помощью нашего [приложения SAML Test:][samltest]

* Обновление имени клиента
* Обновление имени политики, *например, B2C_1A_signup_signin_saml*
* Укажите этот эмитент URI:`https://contoso.onmicrosoft.com/app-name`

Выберите **Войти,** и вы должны быть представлены с пользователем входа в экран. При входе в систему, утверждение SAML выдается обратно в выборку приложения.

## <a name="sample-policy"></a>Пример политики

Мы предоставляем полную политику образца, которую вы можете использовать для тестирования с помощью SAML Test App.

1. Скачать [политику samL-SP-инициативы для входа в систему](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Обновление `TenantId` в соответствии с вашим именем арендатора, например, *contoso.b2clogin.com*
1. Сохранить имя политики *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Поддерживаемые и неподдерживаемые механизмы SAML

Следующие сценарии полагаемой стороны SAML (RP) поддерживаются через вашу собственную конечную точку метаданных:

* Несколько URL-адресов входа в систему или привязка POST для URL-адреса входа в основной объект приложения/обслуживания.
* Укажите ключ подписи для проверки запросов RP в основном объекте приложения/обслуживания.
* Укажите ключ шифрования токенов в главном объекте приложения/сервиса.
* Логины, инициированные поставщиком идентификационных данных, в настоящее время не поддерживаются в выпуске предварительного просмотра.

## <a name="next-steps"></a>Дальнейшие действия

- Более подробную информацию о протоколе SAML вы можете найти [на веб-сайте OASIS.](https://www.oasis-open.org/)
- Получите веб-приложение SAML-теста от [репо сообщества Azure AD B2C GitHub.](https://github.com/azure-ad-b2c/saml-sp-tester)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
