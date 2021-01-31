---
title: Мониторинг поведения пользователей с помощью Application Insights
titleSuffix: Azure AD B2C
description: Сведения о том, как включить журналы событий в Application Insights Azure AD B2C пути взаимодействия пользователя.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218559"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Отслеживание поведения пользователей в Azure Active Directory B2C с использованием Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) поддерживает отправку данных событий непосредственно в [Application Insights](../azure-monitor/app/app-insights-overview.md) с помощью ключа инструментирования, предоставленного для Azure AD B2C. С помощью Application Insights технического профиля можно получить подробные и настраиваемые журналы событий для пути взаимодействия пользователя:

* Получить сведения о поведении пользователей.
* Устранить проблемы в собственных политиках в среде разработки или в рабочей среде.
* Измерить производительность.
* Создать уведомления из Application Insights.

## <a name="overview"></a>Обзор

Чтобы включить пользовательские журналы событий, добавьте Application Insights технический профиль. В техническом профиле вы определяете Application Insights ключ инструментирования, имя события и утверждения для записи. Чтобы опубликовать событие, технический профиль добавляется в качестве этапа оркестрации в [пути взаимодействия пользователя](userjourneys.md).

При использовании Application Insights учитывайте следующее.

- Если новые журналы доступны в Application Insights, обычно это менее чем за пять минут.
- Azure AD B2C позволяет выбрать утверждения для записи. Не включайте утверждения с личными данными.
- Для записи сеанса пользователя события могут быть объединены с помощью идентификатора корреляции. 
- Вызывайте Application Insights технический профиль непосредственно из [пути взаимодействия пользователя](userjourneys.md) или из [ветви](subjourneys.md). Не используйте Application Insights технический профиль в качестве [технического профиля проверки](validation-technical-profile.md).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

При использовании Application Insights с Azure AD B2C потребуется только создать ресурс и получить ключ инструментирования. Дополнительные сведения см. в разделе [создание Application Insights ресурса](../azure-monitor/app/create-new-resource.md) .

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий подписку Azure, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий подписку. Этот клиент не является вашим клиентом Azure AD B2C.
3. Выберите **Создать ресурс** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Application Insights**.
4. Нажмите кнопку **Создать**.
5. Введите **Имя** для ресурса.
6. Из списка **Application type** (Тип приложения) выберите **Веб-приложение ASP.NET**.
7. Для **Группы ресурсов** выберите существующую группу или укажите имя, чтобы создать новую.
8. Нажмите кнопку **Создать**.
4. Создав ресурс Application Insights, откройте его, разверните **Основные компоненты** и скопируйте ключ инструментирования.

![Обзор Application Insights и ключ инструментирования](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Определение утверждений

Утверждение предоставляет временное хранилище данных во время выполнения политики Azure AD B2C. [Схема утверждений](claimsschema.md) — это место, где вы объявляете свои утверждения.

1. Откройте файл расширения для политики, например <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Найдите элемент [ClaimsSchema](claimsschema.md). Если такой элемент не существует, добавьте его.
1. Добавьте следующие утверждения в элемент **ClaimsSchema**. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Добавление новых технических профилей

Технические профили можно рассматривать как функции в пользовательской политике. В этой таблице определены технические профили, используемые для открытия сеанса и публикации событий. Решение использует технический подход к [включению профиля](technicalprofiles.md#include-technical-profile) . Где технический профиль включает еще один технический профиль для изменения параметров или добавления новых функций.

| Технический профиль | Задача |
| ----------------- | -----|
| AppInsights-Common | Общий технический профиль с общим набором конфигураций. Включая, Application Insights ключ инструментирования, коллекцию утверждений для записи и режим разработчика. Следующие технические профили включают в себя общий технический профиль и добавляют дополнительные заявки, например имя события. |
| AppInsights-SignInRequest | Записывает `SignInRequest` событие с набором утверждений при получении запроса на вход. |
| AppInsights-UserSignUp | Записывает `UserSignUp` событие, когда пользователь запускает функцию регистрации в пути регистрации или входа. |
| AppInsights-SignInComplete | Записывает `SignInComplete` событие при успешном завершении проверки подлинности, когда маркер был отправлен в приложение проверяющей стороны. |

Добавляет профили в файл из стартового пакета *TrustFrameworkExtensions.xml*. Добавляет в элемент **ClaimsProviders** следующие элементы.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Замените ключ инструментирования в техническом профиле `AppInsights-Common` идентификатором GUID, который предоставляется ресурсом Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Добавление технических профилей в качестве шагов оркестрации

Вызовите `AppInsights-SignInRequest` в качестве 2 шага оркестрации для отслеживания получения запроса на вход или регистрацию.

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Непосредственно *перед* шагом оркестрации `SendClaims` добавьте новый шаг, который вызывает `AppInsights-UserSignup`. Он активируется, когда пользователь нажимает кнопку "Регистрация" в пути взаимодействия регистрации и входа.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Сразу после шага регистрации `SendClaims` вызовите `AppInsights-SignInComplete`. Этот шаг указывает на успешное завершение пути взаимодействия.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> После добавления новых шагов оркестрации повторно последовательно пронумеруйте шаги, не пропуская ни одного целого числа от 1 до N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Отправка файла, выполнение политики и просмотр событий

Сохраните и отправьте файл *TrustFrameworkExtensions.xml*. Затем вызовите политику проверяющей стороны из своего приложения или используйте **Запустить сейчас** на портале Azure. Подождите минуту, и ваши события будут доступны в Application Insights.

1. Откройте **Application Insights** ресурс в клиенте Azure Active Directory.
2. Выберите **Использование**, а затем выберите **события**.
3. Установите для поля **Во время** значение **Последний час**, а для поля **By** (До) — **3 минуты**.  Возможно, для просмотра результатов понадобится выбрать элемент **Обновить**.

![Использование Application Insights. Колонка "События".](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Сбор дополнительных данных

Чтобы удовлетворить потребности вашего бизнеса, вам может потребоваться записать больше заявок. Чтобы добавить утверждение, сначала [Определите утверждение](#define-claims), а затем добавьте утверждение в коллекцию входных утверждений. Утверждения, добавляемые в технический профиль *AppInsights-Common* , будут отображаться во всех событиях. Утверждения, добавляемые в конкретный технический профиль, будут отображаться только в этом событии. Элемент входящего утверждения содержит следующие атрибуты:

- **Claimtypereferenceid соединяется** — ссылка на тип утверждения. 
- **Партнерклаимтипе** — имя свойства, которое отображается в Azure Insights. Используйте синтаксис `{property:NAME}`, в котором свойство `NAME` добавляется в событие.
- **DefaultValue** — предопределенное записываемое значение, например имя события. Утверждение, используемое в пути взаимодействия пользователя, например имя поставщика удостоверений. Если утверждение пустое, будет использоваться значение по умолчанию. Например, `identityProvider` утверждение задается техническими профилями Федерации, такими как Facebook. Если утверждение пустое, оно указывает на вход пользователя с помощью локальной учетной записи. Таким же значением по умолчанию задается значение *Local*. Кроме того, можно записывать [арбитры утверждений](claim-resolver-overview.md) с контекстным значением, например идентификатором приложения или IP-адресом пользователя.

### <a name="manipulating-claims"></a>Управление утверждениями

[Преобразования входящих утверждений](custom-policy-trust-frameworks.md#manipulating-your-claims) можно использовать для изменения входных утверждений или создания новых перед отправкой в Application Insights. В следующем примере технический профиль включает преобразование *чеккисадмин* input Claims. 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Добавление событий

Чтобы добавить событие, создайте новый технический профиль, включающий технический профиль *AppInsights-Common* . Затем добавьте технический профиль в качестве этапа оркестрации для пути взаимодействия [пользователя](custom-policy-trust-frameworks.md#orchestration-steps). Используйте [предварительное условие](userjourneys.md#preconditions) для активации события при необходимости. Например, сообщите о событии только в том случае, если пользователи работают через MFA.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Теперь, когда у вас есть технический профиль, добавьте событие к пути взаимодействия пользователя. Затем перенумеровать шаги последовательно без пропуска целых чисел от 1 до N.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Включение режима разработчика

При использовании Application Insights для определения событий можно указать, включен ли режим разработчика. Режим разработчика определяет способ буферизации событий. В среде разработки с минимальным объемом событий включение режима разработчика приводит к тому, что события немедленно отправляются на Application Insights. Значение по умолчанию — `false`. Не включайте режим разработчика в рабочих средах.

Чтобы включить режим разработчика, в техническом профиле *AppInsights-Common* измените `DeveloperMode` метаданные на `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Отключение данных телеметрии

Чтобы отключить журналы Application Insights, в техническом профиле *AppInsights-Common* измените `DisableTelemetry` метаданные на `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [создавать настраиваемые панели мониторинга ключевых показателей эффективности с помощью Azure Application Insights](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end