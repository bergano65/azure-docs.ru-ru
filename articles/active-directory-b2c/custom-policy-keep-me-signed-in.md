---
title: Всегда оставаться в системе Azure Active Directory B2C
description: Настройка функции "Оставаться в системе" в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 84ba68c97f69872e39121915a6edf23aa029fa75
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161692"
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

Чтобы включить функции "оставаться, задайте для элемента определения содержимого `DataUri` [идентификатор страницы](contentdefinitions.md#datauri) `unifiedssp` и [версию страницы](page-layout.md) *1.1.0* или выше.

1. Откройте файл расширения политики. Например, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em> . Этот файл расширения является одним из файлов политики, включенных в начальный пакет пользовательской политики, который должен быть получен в предварительных требованиях, приступить [к работе с настраиваемыми политиками](custom-policy-get-started.md).
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
    
1. Сохраните файл расширений.



## <a name="configure-a-relying-party-file"></a>Настройка файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Откройте файл настраиваемой политики. Например, *SignUpOrSignin.xml*.
1. Если он еще не существует, добавьте `<UserJourneyBehaviors>` дочерний узел в узел `<RelyingParty>`. Он должен располагаться сразу после `<DefaultUserJourney ReferenceId="User journey Id" />`, например: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Добавьте следующий узел в качестве дочернего узла элемента `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **Сессионекспиритипе** — указывает, как сеанс расширяется на время, указанное в `SessionExpiryInSeconds` и `KeepAliveInDays`. Значение `Rolling` (по умолчанию) указывает, что сеанс расширен каждый раз, когда пользователь выполняет проверку подлинности. Значение `Absolute` указывает, что пользователь вынужден выполнить повторную проверку подлинности по истечении указанного периода времени.
 
    - **Сессионекспиринсекондс** — время существования файлов cookie сеанса, когда не включена поддержка *входа* , или если пользователь не включил параметр " *оставаться в*системе". Срок действия сеанса истекает после передачи `SessionExpiryInSeconds` или закрытия браузера.
 
    - **Кипаливеиндайс** — время существования файлов cookie сеанса, когда параметр " *оставаться* в системе" включен и пользователь выбрал параметр " *оставаться в*системе".  Значение `KeepAliveInDays` имеет приоритет над значением `SessionExpiryInSeconds` и определяет время истечения срока действия сеанса. Если пользователь закрывает браузер и снова открывает его позже, он по-прежнему может без вмешательства пользователя войти в систему, если он находится в течение периода времени Кипаливеиндайс.
    
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

4. Сохраните изменения и отправьте файл.
5. Чтобы протестировать отправленную настраиваемую политику, на портале Azure перейдите к странице политики и нажмите кнопку **Выполнить**.

Пример политики можно найти [здесь](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
