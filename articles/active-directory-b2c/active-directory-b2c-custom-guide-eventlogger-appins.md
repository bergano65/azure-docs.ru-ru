---
title: Отслеживание поведения пользователей с помощью событий в Application Insights из Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как включить журнал событий в Application Insights из путей взаимодействия пользователей Azure AD B2C с использованием настраиваемых политик (предварительная версия).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ced100f0bdd20841648ca84dfcab1847bdcd3096
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362489"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Отслеживание поведения пользователей в Azure Active Directory B2C с использованием Application Insights

Используя Azure Active Directory (Azure AD) B2C вместе с Azure Application Insights, можно получить подробные настраиваемые журналы событий для путей взаимодействия пользователя. В этой статье раскрываются следующие темы:

* Получить сведения о поведении пользователей.
* Устранить проблемы в собственных политиках в среде разработки или в рабочей среде.
* Измерить производительность.
* Создать уведомления из Application Insights.

> [!NOTE]
> Эта функция предоставляется в предварительной версии.

## <a name="how-it-works"></a>Принцип работы

Identity Experience Framework в Azure AD B2C включает поставщика `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`. Он отправляет данные о событиях непосредственно в Application Insights с использованием ключа инструментирования, предоставляемого в Azure AD B2C.

Этот поставщик использует технический профиль для определения события из Azure AD B2C. Профиль определяет имя события, утверждения, которые записаны, и ключ инструментирования. Чтобы опубликовать событие, технический профиль добавляется как `orchestration step` или `validation technical profile` в настраиваемый путь взаимодействия пользователя.

Application Insights может объединить события с использованием идентификатора корреляции для записи сеанса пользователя. Application Insights делает событие и сеанс доступными в течение нескольких секунд и предоставляет множество инструментов визуализации, экспорта и аналитики.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md). В этой статье предполагается, что вы используете начальный пакет настраиваемой политики. Однако он не является обязательным.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

При использовании Application Insights с Azure AD B2C потребуется только создать ресурс и получить ключ инструментирования.

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Убедитесь, что используете каталог, содержащий клиент подписки Azure, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий вашу подписку. Этот клиент не является вашим клиентом Azure AD B2C.
3. Выберите **Создать ресурс** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Application Insights**.
4. Нажмите кнопку **Создать**.
5. Введите **Имя** для ресурса.
6. Из списка **Application type** (Тип приложения) выберите **Веб-приложение ASP.NET**.
7. Для **Группы ресурсов** выберите существующую группу или укажите имя, чтобы создать новую.
8. Нажмите кнопку **Создать**.
4. Создав ресурс Application Insights, откройте его, разверните **Основные компоненты** и скопируйте ключ инструментирования.

![Обзор Application Insights и ключ инструментирования](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Добавление новых определений ClaimType

В начальном пакете откройте файл *TrustFrameworkExtensions.xml* и добавьте в элемент [BuildingBlocks](buildingblocks.md) следующее.

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>Добавление новых технических профилей

Технические профили можно считать функциями в Identity Experience Framework Azure AD B2C. В этой таблице определены технические профили, используемые для открытия сеанса и публикации событий.

| Технический профиль | Задача |
| ----------------- | -----|
| AzureInsights-Common | Создает общий набор параметров, который будет включен во все технические профили AzureInsights. | 
| AzureInsights-SignInRequest | Создает событие SignIn с набором требований при получении запроса на вход. | 
| AzureInsights-UserSignup | Создает событие UserSignup, когда пользователь запускает регистрацию в пути взаимодействия регистрации или входа. | 
| AzureInsights-SignInComplete | Записывает успешное выполнение проверки подлинности при передаче токена в приложение проверяющей стороны. | 

Добавляет профили в файл из стартового пакета *TrustFrameworkExtensions.xml*. Добавляет в элемент **ClaimsProviders** следующие элементы.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Замените ключ инструментирования в техническом профиле `ApplicationInsights-Common` идентификатором GUID, который предоставляется ресурсом Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Добавление технических профилей в качестве шагов оркестрации

Вызовите `Azure-Insights-SignInRequest` в качестве 2 шага оркестрации для отслеживания получения запроса на вход или регистрацию.

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Непосредственно *перед* шагом оркестрации `SendClaims` добавьте новый шаг, который вызывает `Azure-Insights-UserSignup`. Он активируется, когда пользователь нажимает кнопку "Регистрация" в пути взаимодействия регистрации и входа.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Сразу после шага регистрации `SendClaims` вызовите `Azure-Insights-SignInComplete`. Этот шаг указывает на успешное завершение пути взаимодействия.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> После добавления новых шагов оркестрации повторно последовательно пронумеруйте шаги, не пропуская ни одного целого числа от 1 до N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Отправка файла, выполнение политики и просмотр событий

Сохраните и отправьте файл *TrustFrameworkExtensions.xml*. Затем вызовите политику проверяющей стороны из своего приложения или используйте **Запустить сейчас** на портале Azure. За считаные секунды события будут доступны в Application Insights.

1. Откройте ресурс **Application Insights** в своем клиенте Azure Active Directory.
2. Выберите **Использование** > **События**.
3. Установите для поля **Во время** значение **Последний час**, а для поля **By** (До) — **3 минуты**.  Возможно, для просмотра результатов понадобится выбрать элемент **Обновить**.

![Использование Application Insights. Колонка "События".](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Дополнительная информация

Добавьте типы утверждений и события в путь взаимодействия пользователя в соответствии со своими потребностями. Можно использовать [claim resolvers](claim-resolver-overview.md) (Арбитры утверждений) или любой другой тип строковых утверждений. Также утверждения можно добавлять путем добавления элемента **Input Claim** (Входящее утверждение) в событие Application Insights или в технический профиль AzureInsights-Common. 

- **ClaimTypeReferenceId** — это ссылка на тип утверждения.
- **PartnerClaimType** — имя свойства, отображаемое в Azure Insights. Используйте синтаксис `{property:NAME}`, в котором свойство `NAME` добавляется в событие. 
- **DefaultValue** — используйте любой строковой параметр или арбитр утверждений. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

