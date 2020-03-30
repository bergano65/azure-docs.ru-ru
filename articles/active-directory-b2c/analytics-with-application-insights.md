---
title: Отслеживайте поведение пользователей с помощью приложений Insights
titleSuffix: Azure AD B2C
description: Узнайте, как включить журналы событий в Application Insights от пользователей Azure AD B2C с помощью пользовательских политик.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 03/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 687c9620ae70f7bca2b95a94dd8fe411d7348b30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246489"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Отслеживание поведения пользователей в Azure Active Directory B2C с использованием Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Активный каталог Azure B2C (Azure AD B2C) поддерживает отправку данных о событиях непосредственно в [Application Insights](../azure-monitor/app/app-insights-overview.md) с помощью ключа приборов, предоставленного Azure AD B2C.  С помощью технического профиля Application Insights вы можете получить подробные и настраиваемые журналы событий для поездок пользователя в:

* Получить сведения о поведении пользователей.
* Устранить проблемы в собственных политиках в среде разработки или в рабочей среде.
* Измерить производительность.
* Создать уведомления из Application Insights.

## <a name="how-it-works"></a>Принцип работы

Технический профиль [Application Insights](application-insights-technical-profile.md) определяет событие от Azure AD B2C. Профиль определяет имя события, утверждения, которые записаны, и ключ инструментирования. Чтобы опубликовать событие, технический профиль добавляется в качестве шага оркестровки в [путешествии пользователя.](userjourneys.md)

Application Insights может объединить события с использованием идентификатора корреляции для записи сеанса пользователя. Application Insights делает событие и сеанс доступными в течение нескольких секунд и предоставляет множество инструментов визуализации, экспорта и аналитики.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

При использовании Application Insights с Azure AD B2C потребуется только создать ресурс и получить ключ инструментирования. Для получения информации [см.](../azure-monitor/app/create-new-resource.md)

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий подписку Azure, выбрав фильтр **подписки каталога** в верхнем меню и выбрав каталог, содержащий подписку. Этот клиент не является вашим клиентом Azure AD B2C.
3. Выберите **Создать ресурс** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Application Insights**.
4. Нажмите кнопку **Создать**.
5. Введите **Имя** для ресурса.
6. Из списка **Application type** (Тип приложения) выберите **Веб-приложение ASP.NET**.
7. Для **Группы ресурсов** выберите существующую группу или укажите имя, чтобы создать новую.
8. Нажмите кнопку **Создать**.
4. Создав ресурс Application Insights, откройте его, разверните **Основные компоненты** и скопируйте ключ инструментирования.

![Обзор Application Insights и ключ инструментирования](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Определение претензий

Претензия предусматривает временное хранение данных во время выполнения политики Azure AD B2C. [Схема претензий](claimsschema.md) — это место, где вы объявляете свои претензии.

1. Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Найдите элемент [ClaimsSchema.](claimsschema.md) Если такой элемент не существует, добавьте его.
1. Добавьте следующие претензии к элементу **ClaimsSchema.** 

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

Технические профили можно считать функциями в Identity Experience Framework Azure AD B2C. В этой таблице определены технические профили, используемые для открытия сеанса и публикации событий.

| Технический профиль | Задача |
| ----------------- | -----|
| AppInsights-Общие | Общий набор параметров, которые будут включены во все технические профили Azure Insights. |
| AppInsights-SignInRequest | Записывает `SignInRequest` событие с набором претензий при получении запроса на ввоза. |
| AppInsights-UserSignUp | Записывает `UserSignUp` событие, когда пользователь запускает опцию регистрации в путешествии регистрации/регистрации. |
| AppInsights-SignInComplete | Записывает `SignInComplete` событие об успешном завершении проверки подлинности, когда токен был отправлен в приложение для опирающихся сторон. |

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
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
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
<OrchestrationStep Order="1" Type="ClaimsExchange">
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

Сохраните и отправьте файл *TrustFrameworkExtensions.xml*. Затем вызовите политику проверяющей стороны из своего приложения или используйте **Запустить сейчас** на портале Azure. За считаные секунды события будут доступны в Application Insights.

1. Откройте ресурс **Application Insights** в арендаторе Active Directory Azure.
2. Выберите**события** **использования** > .
3. Установите для поля **Во время** значение **Последний час**, а для поля **By** (До) — **3 минуты**.  Возможно, для просмотра результатов понадобится выбрать элемент **Обновить**.

![Использование Application Insights. Колонка "События".](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>(Необязательно) Сбор дополнительных данных

Добавьте типы утверждений и события в путь взаимодействия пользователя в соответствии со своими потребностями. Можно использовать [разрешители претензий](claim-resolver-overview.md) или любой тип претензии строки, добавляя элемент **ввода претензии** в событие Application Insights или в технический профиль AppInsights-Common.

- **ClaimTypeReferenceId** — это ссылка на тип утверждения.
- **PartnerClaimType** — имя свойства, отображаемое в Azure Insights. Используйте синтаксис `{property:NAME}`, в котором свойство `NAME` добавляется в событие.
- **DefaultValue** — используйте любой строковой параметр или арбитр утверждений.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Дальнейшие действия

- Подробнее о техническом профиле [Application Insights](application-insights-technical-profile.md) читайте в справке IEF. 