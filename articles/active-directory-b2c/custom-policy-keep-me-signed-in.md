---
title: Держите меня в подписи в Azure Активный каталог B2C
description: Настройка функции "Оставаться в системе" в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0de94cdce1d7f0e9da9d2844b300956ad6f6970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330837"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Включение функции "Оставаться в системе" в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Вы можете включить функциональность Keep Me Signed In (KMSI) для пользователей веб- и нативных приложений, которые имеют локальные учетные записи в каталоге Azure Active Directory B2C (Azure AD B2C). Эта функция предоставляет доступ пользователям, возвращающимся в ваше приложение, не побуждая их повторно вводить свое имя пользователя и пароль. Доступ отменяется, когда пользователь выходит из системы.

Пользователям не следует включать этот параметр на общедоступных компьютерах.

![Пример записаться на страницу регистрации, показывающую подпись Keep me в флажке](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Предварительные требования

- Арендатор Azure AD B2C, настроенный на локальный учетный акт. KMSI не поддерживается для внешних учетных записей поставщика идентификационных данных.
- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Настройка идентификатора страницы

Чтобы включить KMSI, установите элемент `DataUri` определения содержимого в [идентификатор](contentdefinitions.md#datauri) `unifiedssp` страницы и [версию страницы](page-layout.md) *1.1.0* или выше.

1. Откройте файл расширения политики. Например, <em> `SocialAndLocalAccounts/` </em>. Этот файл расширения является одним из файлов политики, включенных в пользовательский пакет стартера политики, который вы должны были получить в предпосылке, [Начать работу с пользовательских политик.](custom-policy-get-started.md)
1. Найдите элемент **BuildingBlocks**. Если такой элемент не существует, добавьте его.
1. Добавьте элемент **ContentDefinitions** в элемент **политики BuildingBlocks.**

    Пользовательская политика должна выглядеть следующим фрагментом кода:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Добавление метаданных в самоутверждаемый технический профиль

Чтобы добавить флажок KMSI в страницу регистрации `setting.enableRememberMe` и регистрации, установите метаданные на ложные. Переиздайте технические профили SelfAsserted-LocalAccountSignin-Email в файле расширения.

1. Найдите элемент ClaimsProviders. Если такой элемент не существует, добавьте его.
1. Добавьте следующие поставщик претензий в элемент ClaimsProviders:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Сохраните файл расширений.

## <a name="configure-a-relying-party-file"></a>Настройка файла опирающейся партии

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Откройте файл настраиваемой политики. Например, *SignUpOrSignin.xml*.
1. Если он еще не существует, `<UserJourneyBehaviors>` добавьте в `<RelyingParty>` узла узла узла узла детский узла. Он должен быть `<DefaultUserJourney ReferenceId="User journey Id" />`расположен сразу `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`после, например: .
1. Добавьте следующий узел в качестве дочернего узла элемента `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExPiryType** - Указывает, как сеанс продлевается `SessionExpiryInSeconds` `KeepAliveInDays`на время, указанное в и . Значение `Rolling` (по умолчанию) указывает на то, что сеанс продлевается каждый раз, когда пользователь выполняет аутентификацию. Значение `Absolute` указывает на то, что пользователь вынужден повторно аутентифицировать сяпотливую после указанного периода времени.

    - **SessionExInSeconds** - Срок службы сессии куки, когда *держать меня в вписано,* не включен, или если пользователь не выбирает *держать меня в.* Сеанс истекает после `SessionExpiryInSeconds` пройдена или браузер закрыт.

    - **KeepAliveInDays** - Срок службы сессии куки, когда *держать меня в вписано* и пользователь выбирает *держать меня в.*  Значение `KeepAliveInDays` имеет приоритет над значением `SessionExpiryInSeconds` и диктует время истечения сеанса. Если пользователь закрывает браузер и вновь открывает его позже, он все еще может бесшумно войти в систему до тех пор, пока он находится в период времени KeepAliveInDays.

    Для получения дополнительной [информации](relyingparty.md#userjourneybehaviors)см.

Мы рекомендуем установить значение SessionExpiryInSeconds как короткий период (1200 секунд), в то время как значение KeepAliveInDays может быть установлено на относительно длительный период (30 дней), как показано в следующем примере:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Тестирование политики

1. Сохраните изменения и отправьте файл.
1. Чтобы протестировать загруженную пользовательскую политику на портале Azure, перейдите на страницу политики, а затем выберите **«Выполнить сейчас».**
1. Введите **имя пользователя** и **пароль,** выберите **Держите меня войти,** а затем нажмите войти в **систему**.
1. Вернитесь на портал Azure. Перейдите на страницу политики, а затем выберите **Copy,** чтобы скопировать URL-адрес регистрации.
1. В строке адреса браузера удалите параметр строки `&prompt=login` запроса, который вынуждает пользователя вводить свои учетные данные по этому запросу.
1. В браузере нажмите **Go**. Теперь Azure AD B2C выпустит токен доступа, не попросив вас снова войти в систему. 

## <a name="next-steps"></a>Дальнейшие действия

Найти пример политики [здесь](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
