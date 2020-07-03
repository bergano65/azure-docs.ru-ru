---
title: Всегда оставаться в системе Azure Active Directory B2C
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
ms.openlocfilehash: 041fb8d881307b52fb170a11618f930debc522a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80803166"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Включение функции "Оставаться в системе" в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Вы можете включить функцию "оставаться в системе" (функции "оставаться) для пользователей вашего веб-приложения и собственных приложений с локальными учетными записями в каталоге Azure Active Directory B2C (Azure AD B2C). Эта функция предоставляет пользователям доступ к приложению без запроса на повторное ввод имени пользователя и пароля. Доступ отменяется, когда пользователь выходит из системы.

Пользователям не следует включать этот параметр на общедоступных компьютерах.

![Пример страницы входа, на которой отображается флажок "оставаться в системе"](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Предварительные требования

- Клиент Azure AD B2C, настроенный для разрешения входа в локальную учетную запись. ФУНКЦИИ "оставаться не поддерживается для учетных записей внешних поставщиков удостоверений.
- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Настройка идентификатора страницы

Чтобы включить функции "оставаться, задайте для элемента определения `DataUri` содержимого [идентификатор](contentdefinitions.md#datauri) `unifiedssp` страницы и [версию страницы](page-layout.md) *1.1.0* или выше.

1. Откройте файл расширения политики. Например, <em> `SocialAndLocalAccounts/` </em>. Этот файл расширения является одним из файлов политики, включенных в начальный пакет пользовательской политики, который должен быть получен в предварительных требованиях, приступить [к работе с настраиваемыми политиками](custom-policy-get-started.md).
1. Найдите элемент **BuildingBlocks**. Если такой элемент не существует, добавьте его.
1. Добавьте элемент **ContentDefinitions** в элемент **BuildingBlocks** политики.

    Ваша пользовательская политика должна выглядеть, как в следующем фрагменте кода:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Добавление метаданных в самостоятельно утвержденный технический профиль

Чтобы добавить флажок функции "оставаться на страницу регистрации и входа, установите для `setting.enableRememberMe` метаданных значение true. Переопределите технические профили SelfAsserted-Локалаккаунтсигнин-email в файле расширения.

1. Найдите элемент ClaimsProviders. Если такой элемент не существует, добавьте его.
1. Добавьте в элемент Клаимспровидерс следующий поставщик утверждений:

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

## <a name="configure-a-relying-party-file"></a>Настройка файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Откройте файл настраиваемой политики. Например, *SignUpOrSignin.xml*.
1. Добавьте в `<UserJourneyBehaviors>` `<RelyingParty>` узел дочерний узел, если он еще не создан. Он должен располагаться сразу `<DefaultUserJourney ReferenceId="User journey Id" />`после, например: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Добавьте следующий узел в качестве дочернего узла элемента `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **Сессионекспиритипе** — указывает, как сеанс расширяется на время, заданное `SessionExpiryInSeconds` в `KeepAliveInDays`и. `Rolling` Значение (по умолчанию) указывает, что сеанс расширен каждый раз, когда пользователь выполняет проверку подлинности. `Absolute` Значение указывает, что пользователь вынужден выполнить повторную проверку подлинности по истечении указанного периода времени.

    - **Сессионекспиринсекондс** — время существования файлов cookie сеанса, когда не включена поддержка *входа* , или если пользователь не включил параметр " *оставаться в*системе". Срок действия сеанса истекает `SessionExpiryInSeconds` после передачи или закрытия браузера.

    - **Кипаливеиндайс** — время существования файлов cookie сеанса, когда параметр " *оставаться* в системе" включен и пользователь выбрал параметр " *оставаться в*системе".  Значение `KeepAliveInDays` имеет приоритет над `SessionExpiryInSeconds` значением и определяет время истечения срока действия сеанса. Если пользователь закрывает браузер и снова открывает его позже, он по-прежнему может без вмешательства пользователя войти в систему, если он находится в течение периода времени Кипаливеиндайс.

    Дополнительные сведения см. в разделе [поведение пути взаимодействия пользователя](relyingparty.md#userjourneybehaviors).

Рекомендуется установить значение Сессионекспиринсекондс на короткий период (1200 секунд), а значение Кипаливеиндайс можно задать относительно длительный период (30 дней), как показано в следующем примере:

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
1. Чтобы проверить отправленную вами пользовательскую политику, в портал Azure перейдите на страницу политика и выберите **Запустить сейчас**.
1. Введите **имя пользователя** и **пароль**, установите флажок **оставаться в**системе и нажмите кнопку **Вход**.
1. Вернитесь на портал Azure. Перейдите на страницу политика и нажмите кнопку **Копировать** , чтобы скопировать URL-адрес входа.
1. В адресной строке браузера удалите параметр строки `&prompt=login` запроса, который заставляет пользователя вводить учетные данные для этого запроса.
1. В браузере щелкните Go ( **Переход**). Теперь Azure AD B2C выдаст маркер доступа без запроса на вход. 

## <a name="next-steps"></a>Дальнейшие действия

Найдите пример политики [здесь](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
