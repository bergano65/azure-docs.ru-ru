---
title: Настройка Azure AD B2C в качестве IdP SAML для приложений
title-suffix: Azure AD B2C
description: Настройка Azure AD B2C для предоставления приложениям (поставщикам служб) утверждений протокола SAML. Azure AD B2C будет использоваться в качестве единого поставщика удостоверений (IdP) для приложения SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6f7888e978fd4eb19232c156ce65b6e4967d9c5a
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94575974"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Регистрация приложения SAML в Azure AD B2C

Из этой статьи вы узнаете, как настроить Azure Active Directory B2C (Azure AD B2C) в качестве поставщика удостоверений SAML (IdP) для своих приложений.

## <a name="scenario-overview"></a>Обзор сценария

Организациям, использующим Azure AD B2C в качестве решения для управления удостоверениями и доступом клиентов, может потребоваться взаимодействие с поставщиками удостоверений или приложениями, которые настроены выполнять аутентификацию с помощью протокола SAML.

Azure AD B2C обеспечивает взаимодействие с SAML одним из двух способов:

* Выполняя роль *поставщика удостоверений* (IdP) и обеспечивая единый вход (SSO) с помощью поставщиков служб на основе SAML (ваших приложений).
* Выполняя роль *поставщика служб* и взаимодействуя с поставщиками удостоверений на основе SAML, такими как Salesforce и ADFS

![Схема с B2C в качестве поставщика удостоверений слева и B2C в качестве поставщика служб справа](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Просуммируем два не исключающих друг друга базовых сценария использования SAML:

| Сценарий | Роль Azure AD B2C | Практическое руководство |
| -------- | ----------------- | ------- |
| Мое приложение ожидает, что утверждение SAML выполнит аутентификацию. | **Azure AD B2C выступает в качестве поставщика удостоверений (IdP)**<br />Azure AD B2C выступает в качестве поставщика удостоверений SAML для приложений. | Эта статья |
| Моим пользователям требуется единый вход, который бы обеспечивался поставщиком удостоверений, соответствующим стандартам SAML, таким как ADFS, Salesforce или Shibboleth.  | **Azure AD B2C выступает в качестве поставщика служб**<br />Azure AD B2C выступает в качестве поставщика служб при подключении к поставщику удостоверений SAML. Между приложением и поставщиком удостоверений SAML работает прокси-сервер федерации.  | <ul><li>[Настройка входа с помощью ADFS в качестве поставщика удостоверений SAML с помощью настраиваемых политик](identity-provider-adfs2016-custom.md)</li><li>[Настройка входа с помощью поставщика SAML Salesforce и с применением пользовательских политик](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Предварительные требования

* Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure AD B2C](custom-policy-get-started.md). Вам потребуется настраиваемая политика *SocialAndLocalAccounts* из начального пакета настраиваемых политик, который обсуждается в этой статье.
* Базовое понимание протокола SAML.
* Веб-приложение, настроенное в качестве поставщика служб SAML. В рамках этого руководства вы можете использовать предоставляемое нами [тестовое приложение SAML][samltest].

## <a name="components-of-the-solution"></a>Компоненты решения

Для этого сценария требуется три основных компонента:

* **Поставщик служб** SAML с возможностью отправлять запросы SAML, а также получать от Azure AD B2C утверждения SAML, декодировать их и реагировать на них. Поставщик услуг называется также приложением проверяющей стороны.
* Общедоступная **конечная точка метаданных** SAML для вашего поставщика служб.
* [клиент Azure AD B2C](tutorial-create-tenant.md);

Если у вас еще нет поставщика служб SAML и соответствующей конечной точки метаданных, можно использовать пример приложения SAML, который мы предоставляем для тестирования:

[Тестовое приложение SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Настройка сертификатов

Чтобы создать отношение доверия между поставщиком служб и Azure AD B2C, необходимо предоставить сертификаты веб-приложения X509.

* **Сертификаты поставщика служб**
  * Сертификат с закрытым ключом, хранящимся в вашем веб-приложении. Этот сертификат используется вашим поставщиком служб для подписи запроса SAML, отправляемого в Azure AD B2C. Azure AD B2C считывает открытый ключ из метаданных поставщика служб для проверки подписи.
  * (Дополнительно.) Сертификат с закрытым ключом, хранящимся в вашем веб-приложении. Azure AD B2C считывает открытый ключ из метаданных поставщика служб для шифрования утверждения SAML. Затем поставщик служб использует закрытый ключ для расшифровки утверждения.
* **Сертификаты Azure AD B2C**
  * Сертификат с закрытым ключом в Azure AD B2C. Этот сертификат используется Azure AD B2C для подписи ответа SAML, отправляемого поставщику служб. Поставщик служб считывает открытый ключ метаданных Azure AD B2C для проверки подписи ответа SAML.

Можно использовать сертификат, выданный общедоступным центром сертификации или (для этого учебника) самозаверяющим сертификатом.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Подготовка самозаверяющего сертификата

Если у вас еще нет сертификата, для работы с этим учебником можно использовать самозаверяющий сертификат. В Windows для создания сертификата можно использовать командлет PowerShell [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate).

1. Выполните эту команду PowerShell, чтобы создать самозаверяющий сертификат. Измените аргумент `-Subject` соответствующим образом для своего приложения и имени клиента Azure AD B2C. Можно также скорректировать дату `-NotAfter`, чтобы указать другой срок действия сертификата.

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

1. Откройте **Управление сертификатами пользователя** > **Текущий пользователь** > **Персональные** > **Сертификаты** > *yourappname.yourtenant.onmicrosoft.com*.
1. Выберите сертификат > **Действие** > **Все задачи** > **Экспорт**.
1. Нажмите **Да** > **Далее** > **Да, экспортировать закрытый ключ** > **Далее**.
1. Примите значения по умолчанию для **формата файла экспорта**.
1. Укажите пароль для сертификата.

### <a name="12-upload-the-certificate"></a>1.2 Загрузка сертификата

Затем отправьте утверждение SAML и сертификат подписи ответа в Azure AD B2C.

1. Выполните вход на [портал Azure](https://portal.azure.com) и найдите свой клиент Azure AD B2C.
1. В разделе **Политики** выберите **Identity Experience Framework** и **Ключи политики**.
1. Нажмите **Добавить** и выберите **Параметры** > **Отправка**.
1. Введите **Имя** , например *SamlIdpCert*. Префикс *B2C_1A_* будет автоматически добавлен к имени ключа.
1. Отправьте сертификат, используя элемент управления отправкой файлов.
1. Укажите пароль для сертификата.
1. Нажмите кнопку **создания**.
1. Убедитесь, что ключ отображается должным образом. Например, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Подготовка политики

### <a name="21-create-the-saml-token-issuer"></a>2.1 Создайте издатель токенов SAML

Теперь добавьте для своего клиента возможность издавать токены SAML, используя технические профили [издателя токенов SAML](saml-issuer-technical-profile.md) и [поставщика сеансов SAML](custom-policy-reference-sso.md#samlssosessionprovider).

Откройте `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** в начальном пакете настраиваемых политик.

Откройте раздел `<ClaimsProviders>` и добавьте следующий фрагмент кода XML.

Можно изменить значение метаданных `IssuerUri`. Это URI издателя, который возвращается в ответе SAML от Azure AD B2C. Приложение проверяющей стороны должно быть настроено принимать URI издателя в процессе проверки утверждений SAML.

```xml
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

## <a name="3-add-the-saml-relying-party-policy"></a>3. Добавление политики проверяющей стороны SAML

Теперь, когда клиент может выдавать утверждения SAML, необходимо создать политику проверяющей стороны SAML и изменить путь взаимодействия пользователя, чтобы он выдавал утверждение SAML вместо JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Создание политики регистрации или входа в систему

1. Создайте копию файла *SignUpOrSignin.xml* в рабочем каталоге начального пакета и сохраните ее под новым названием. Например, *SignUpOrSigninSAML.xml*. Это файл политики проверяющей стороны.

1. Откройте файл *SignUpOrSigninSAML.xml* в любом удобном редакторе.

1. Измените `PolicyId` и `PublicPolicyUri` политики на _B2C_1A_signup_signin_saml_ и `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`, как показано ниже.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Добавьте следующий фрагмент XML-кода перед элементом `<RelyingParty>`. Этот XML-код перезаписывает шаг оркестрации 7 взаимодействия пользователя _SignUpOrSignIn_. Если вы начали из другой папки в начальном пакете или настроили путь взаимодействия пользователя, добавив или удалив шаги оркестрации, убедитесь, что число (в элементе `order`) согласовано с указанным во взаимодействии пользователя для шага издателя маркера (например, в других папках начального пакета это шаг 4 для `LocalAccounts`, 6 для `SocialAccounts` и 9 для `SocialAndLocalAccountsWithMfa`).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Замените весь элемент `<TechnicalProfile>` в элементе `<RelyingParty>` следующим XML-кодом технического профиля.

    ```xml
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

1. Обновите `tenant-name`, указав имя клиента Azure AD B2C.

Окончательный файл политики проверяющей стороны должен выглядеть как следующий XML-код:

```xml
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

> [!NOTE]
> При реализации других типов потоков пользователя (например, для входа, сброса пароля или редактирования профиля) процесс по сути аналогичен описанному в этом разделе. На шаге 4 выше вы измените последний шаг пути взаимодействия пользователя с `JWTIssuer` на `Saml2AssertionIssuer` . И на шаге 6 выше в разделе проверяющей стороны вы измените **протокол** с `OpenIdConnect` на `SAML2` .

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Отправка и тестирование метаданных политики

Сохраните изменения и отправьте новый файл политики. После отправки обеих политик (файлы расширения и проверяющей стороны) откройте веб-браузер и перейдите к метаданным политики.

Метаданные IDP политики Azure AD B2C — это сведения, используемые в протоколе SAML для предоставления конфигурации поставщика удостоверений SAML. Метаданные определяют расположение служб, например вход и выход, сертификаты, метод входа и многое другое. Метаданные политики Azure AD B2C доступны по следующему URL-адресу. Замените `tenant-name` именем клиента Azure AD B2C и `policy-name` именем (ID) политики, например.../B2C_1A_SAML2_signup_signin/самлп/Метадата:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Теперь ваша настраиваемая политика и клиент Azure AD B2C готовы. Затем создайте регистрацию приложения в Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Настройка приложения в каталоге Azure AD B2C

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4,1. Регистрация приложения в Azure AD B2C

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы** , а затем найдите и выберите **Azure AD B2C**
1. Щелкните **Регистрация приложений** и выберите **Новая регистрация**.
1. Введите **имя** приложения. Например, *SAMLApp1*.
1. В разделе **Поддерживаемые типы учетных записей** выберите **Учетные записи только в этом каталоге организации**.
1. В разделе **URI перенаправления** выберите **Интернет** и введите `https://localhost`. Это значение можно изменить позже в манифесте регистрации приложения.
1. Выберите **Зарегистрировать**.

### <a name="42-update-the-app-manifest"></a>4.2 Обновление манифеста приложения

Для приложений SAML необходимо настроить несколько свойств в манифесте регистрации приложения.

1. На [портале Azure](https://portal.azure.com) перейдите к регистрации приложения, созданной в предыдущем разделе.
1. В разделе **Управление** выберите **Манифест** , чтобы открыть редактор манифестов. В следующих разделах будут изменены несколько свойств.

#### <a name="identifieruris"></a>identifierUris

`identifierUris` — это коллекция строк, содержащая определяемые пользователем универсальные коды ресурсов (URI), которые уникально идентифицируют веб-приложение в клиенте Azure AD B2C. URI должен соответствовать имени запроса SAML `Issuer` . Определяемый пользователем универсальный код ресурса обычно имеет то же значение, что и метаданные поставщика услуг `entityID` .

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Это свойство представляет общедоступный URL-адрес метаданных поставщика служб. URL-адрес метаданных может указывать на файл метаданных, передаваемый в любую анонимно доступную конечную точку, например хранилище больших двоичных объектов.

Метаданные — это сведения, используемые в протоколе SAML для предоставления конфигурации стороны SAML, например поставщика служб. Метаданные определяют расположение служб, например вход и выход, сертификаты, метод входа и многое другое. Azure AD B2C считывает метаданные поставщика служб и действует соответствующим образом. Метаданные не требуются. Можно также указать некоторые атрибуты, такие как URI ответа и URI выхода, непосредственно в манифесте приложения.

Если имеются свойства, указанные *и* в URL-адресе метаданных SAML, и в манифесте регистрации приложения, они **объединяются**. Свойства, указанные в URL-адресе метаданных, обрабатываются первыми и имеют приоритет.

Для работы с этим руководством, где используется тестовое приложение SAML, используйте следующее значение для `samlMetadataUrl`.

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (дополнительно)

Если не указать URI метаданных, можно явно указать URL-адрес ответа. Это необязательное свойство представляет `AssertionConsumerServiceUrl` (URL-адрес`SingleSignOnService` в метаданных поставщика служб), а `BindingType` предположительно имеет значение `HTTP POST`.

Если вы решили настроить URL-адрес ответа и URL-адрес выхода в манифесте приложения без использования метаданных поставщика служб, Azure AD B2C не будет проверять подпись запроса SAML или шифровать ответ SAML.

В этом руководстве, в котором используется тестовое приложение SAML, задайте свойству `url` `replyUrlsWithType` значение, показанное в следующем фрагменте кода JSON.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (необязательно)

Это необязательное свойство представляет URL-адрес `Logout` (URL-адрес `SingleLogoutService` в метаданных проверяющей стороны), а `BindingType` для него предположительно имеет значение `Http-Redirect`.

Для работы с этим руководством, где используется тестовое приложение SAML, оставьте для `logoutUrl` значение `https://samltestapp2.azurewebsites.net/logout`.

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Обновление кода приложения

Последним шагом является включение Azure AD B2C в качестве поставщика удостоверений SAML в приложение проверяющей стороны SAML. Все приложения уникальны, поэтому соответствующие пошаговые процедуры тоже различаются. Дополнительные сведения см. в документации приложения.

Метаданные можно настроить в поставщике услуг как "статические метаданные" или "динамические метаданные". В статическом режиме вы копируете все метаданные или их часть из метаданных политики Azure AD B2C. В динамическом режиме необходимо задать URL-адрес метаданных и позволить приложению динамически считывать метаданные.

Обычно требуются некоторые или все перечисленные ниже действия.

* **Метаданные**. `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Issuer** : значение запроса SAML `issuer` должно совпадать с одним из URI, настроенных в `identifierUris` элементе манифеста регистрации приложения. Если имя запроса SAML `issuer` не существует в `identifierUris` элементе, [добавьте его в манифест регистрации приложения](#identifieruris). Например, `https://contoso.onmicrosoft.com/app-name`. 
* **URL-адрес входа/конечная точка SAML/URL-адрес SAML** : проверьте значение в файле МЕТАДАННЫХ политики SAML для `<SingleSignOnService>` XML-элемента Azure AD B2C
* **Сертификат**. Это *B2C_1A_SamlIdpCert* , но без закрытого ключа. Чтобы получить открытый ключ сертификата, выполните следующие действия.

    1. Перейдите по URL-адресу метаданных, указанному выше.
    1. Скопируйте значение в элемент `<X509Certificate>`.
    1. Вставьте его в текстовый файл.
    1. Сохраните текстовый файл под именем *.cer*.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Тестирование с использованием приложения для тестирования SAML (необязательно)

Чтобы завершить работу с этим руководством, используйте [тестовое приложение SAML][samltest].

* Обновление имени клиента
* Обновите имя политики, например *B2C_1A_signup_signin_saml*.
* Укажите этот URI издателя. Используйте один из URI, найденных в `identifierUris` элементе в манифесте регистрации приложения, например `https://contoso.onmicrosoft.com/app-name` .

Выберите **Вход** — отобразится экран входа для пользователей. После входа утверждение SAML возвращается в пример приложения.

## <a name="enable-encrypted-assertions-optional"></a>Включить зашифрованные утверждения (необязательно)

Для шифрования утверждений SAML, отправляемых обратно поставщику услуг, Azure AD B2C будет использовать сертификат открытого ключа поставщика услуг. Открытый ключ должен существовать в метаданных SAML, описанных в приведенном выше [разделе "самлметадатаурл"](#samlmetadataurl) , как кэйдескриптор с использованием шифрования.

Следующий XML-код является примером Кэйдескриптор метаданных SAML с использованием параметра encryption:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Чтобы разрешить Azure AD B2C отправку зашифрованных утверждений, задайте для элемента метаданных **вантсенкриптедассертион** значение `true` в [техническом профиле проверяющей](relyingparty.md#technicalprofile)стороны. Также можно настроить алгоритм, используемый для шифрования утверждения SAML. Дополнительные сведения см. в статье [метаданные технического профиля проверяющей](relyingparty.md#metadata)стороны. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>Включить поток, инициированный поставщиком удостоверений (необязательно)

В потоке, инициированном поставщиком удостоверений, процесс входа инициируется поставщиком удостоверений (Azure AD B2C), который отправляет незапрошенный ответ SAML поставщику услуг (приложение проверяющей стороны). В настоящее время мы не поддерживаем сценарии, в которых инициирующий поставщик удостоверений является внешним поставщиком удостоверений, например [AD-FS](identity-provider-adfs2016-custom.md)или [Salesforce](identity-provider-salesforce-custom.md).

Чтобы включить поток, инициированный поставщиком удостоверений (Azure AD B2C), задайте для элемента метаданных **идпинитиатедпрофилинаблед** значение `true` в [техническом профиле проверяющей](relyingparty.md#technicalprofile)стороны.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Чтобы войти или зарегистрировать пользователя с помощью потока, инициированного поставщиком удостоверений, используйте следующий URL-адрес:

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

Измените следующие значения:

* **имя клиента** с именем клиента
* **Политика — имя** с именем политики проверяющей стороны SAML.
* **app-identifier-URI** с параметром `identifierUris` в файле метаданных, например `https://contoso.onmicrosoft.com/app-name`
## <a name="sample-policy"></a>Пример политики

Мы предоставляем полный пример политики, который можно использовать для тестирования с помощью тестового приложения SAML.

1. Скачайте [пример политики входа, инициируемого SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Обновите `TenantId` в соответствии с именем клиента, например *contoso.b2clogin.com*.
1. Оставьте имя политики *B2C_1A_SAML2_signup_signin*.

## <a name="supported-and-unsupported-saml-modalities"></a>Поддерживаемые и неподдерживаемые модальности SAML

Следующие сценарии проверяющей стороны SAML поддерживаются с помощью собственной конечной точки метаданных:

* Несколько URL-адресов выхода или привязка POST для URL-адреса выхода в объекте приложения или субъекта-службы.
* Укажите ключ подписи, чтобы проверить запросы проверяющей стороны в объекте приложения или субъекта-службы.
* Укажите ключ шифрования маркера в объекте приложения или субъекта-службы.
* Вход, инициированный поставщиком удостоверений, в котором Azure AD B2C поставщик удостоверений.

## <a name="saml-token"></a>маркер SAML

Маркер SAML — это маркер безопасности, выдаваемый Azure AD B2C после успешного входа. Он содержит сведения о пользователе, поставщик службы, для которого предназначен маркер, подпись и срок действия. В следующей таблице перечислены утверждения и свойства, которые могут быть ожидаемыми в маркере SAML, выданном Azure AD B2C.

|Элемент  |Свойство  |Примечания  |
|---------|---------|---------|
|`<Response>`| `ID` | Автоматически созданный уникальный идентификатор ответа. | 
|`<Response>`| `InResponseTo` | Идентификатор запроса SAML, на который отвечает это сообщение. | 
|`<Response>` | `IssueInstant` | Время, по истечении которого отклики отдаются. Значение времени кодируется в формате UTC.  Чтобы изменить параметры времени существования маркера, установите `TokenNotBeforeSkewInSeconds` [метаданные](saml-issuer-technical-profile.md#metadata) технического профиля ИЗДАТЕЛЯ маркера SAML. | 
|`<Response>` | `Destination`| Ссылка URI, указывающая адрес, на который был отправлен этот ответ. Значение идентично запросу SAML `AssertionConsumerServiceURL` . | 
|`<Response>` `<Issuer>` | |Идентифицирует издателя маркера. Это произвольный URI, определяемый `IssuerUri` [метаданными](saml-issuer-technical-profile.md#metadata) проблемы токена SAML.     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |Участник, для которого маркер утверждает сведения, например идентификатор объекта пользователя. Это значение является неизменяемым и не может быть переназначено или повторно использовано. Это значение можно использовать для безопасных проверок авторизации, например, когда маркер используется для доступа к ресурсу. По умолчанию утверждение субъекта заполняется идентификатором объекта пользователя в каталоге.|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | Ссылка URI, представляющая классификацию сведений об идентификаторе на основе строк. По умолчанию это свойство опущено. [Субжектнамингинфо](relyingparty.md#subjectnaminginfo) проверяющей стороны можно задать `NameID` Формат, например `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` . |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |Время, когда токен становится действительным. Значение времени кодируется в формате UTC. Приложение должно использовать это утверждение для проверки действительности срока действия маркера. Чтобы изменить параметры времени существования маркера, установите `TokenNotBeforeSkewInSeconds` [метаданные](saml-issuer-technical-profile.md#metadata) технического профиля для выпуска ошибки маркера SAML. |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | Время, когда маркер станет недействительным. Приложение должно использовать это утверждение для проверки действительности срока действия маркера. Значение составляет 15 минут после `NotBefore` и не может быть изменено.|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |Ссылка URI, которая определяет предполагаемую аудиторию. Он определяет предполагаемого получателя маркера. Значение идентично запросу SAML `AssertionConsumerServiceURL` .|
|`<Response>``<Assertion>` `<AttributeStatement>` коллекция`<Attribute>` | | Коллекция утверждений (утверждения), настроенная в заявках на вывод [технического профиля проверяющей](relyingparty.md#technicalprofile) стороны. Имя утверждения можно настроить, задав `PartnerClaimType` для выходного утверждения. |

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о протоколе SAML [можно найти на веб-сайте OASIS](https://www.oasis-open.org/).
- Получите тестовое веб-приложение SAML из [репозитория сообщества Azure AD B2C GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
