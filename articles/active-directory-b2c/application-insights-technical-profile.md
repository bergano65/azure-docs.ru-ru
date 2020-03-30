---
title: Определение технического профиля Application Insights в пользовательской политике
titleSuffix: Azure AD B2C
description: Определите технический профиль Application Insights в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108579"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Определение технического профиля Application Insights в пользовательской политике Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) поддерживает отправку данных о событиях непосредственно в [Application Insights](../azure-monitor/app/app-insights-overview.md) с помощью ключа приборов, предоставленного Azure AD B2C.  С помощью технического профиля Application Insights вы можете получить подробные и настраиваемые журналы событий для поездок пользователя в:

* Получить сведения о поведении пользователей.
* Устранить проблемы в собственных политиках в среде разработки или в рабочей среде.
* Измерить производительность.
* Создать уведомления из Application Insights.


## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **обработчика** должен содержать полностью квалифицированное название сборки обработчика протоколов, которая используется Azure AD B2C для анализа приложений:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

В следующем примере показан общий технический профиль Application Insights. Другие технические профили Application Insights включают AzureInsights-Common для использования своей конфигурации.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Входящие утверждения

Элемент **InputClaims** содержит список претензий для отправки в Application Insights. Вы также можете сопоставить имя своей претензии на имя, которое вы предпочитаете появляться в Application Insights. Ниже приводится следующий пример, как отправлять телеметрии в Application Insights. Свойства события добавляются через синтаксис, `{property:NAME}`где NAME является свойством, добавляемым к событию. DefaultValue может быть либо статическим значением, либо значением, которое разрешается одним из поддерживаемых [разрешителей претензий.](claim-resolver-overview.md)

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Элемент **InputClaimsTransformations** может содержать коллекцию элементов **InputClaimsTransformation,** которые используются для изменения утверждений ввода или создания новых перед отправкой в Application Insights.

## <a name="persist-claims"></a>Сохранение утверждений

Элемент PersistedClaims не используется.

## <a name="output-claims"></a>Исходящие утверждения

Элементы OutputClaims и OutputClaimsTransformations не используются.

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент CryptographicKeys не используется.


## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| InstrumentationKey| Да | [Инструментальный ключ](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights, который будет использоваться для регистрации событий. | 
| DeveloperMode| нет | Boolean, указывающий, включен ли режим разработчика. Возможные `true` значения: `false` или (по умолчанию). Эти метаданные контролируют, как буферизируются события. В среде разработки с минимальным объемом событий, позволяющий режиму разработчика, приводить к событиям, немедленно отправляемым в Application Insights.|  
|ОтключитьТелеметрия |нет |Булеан, указывающий, должна ли быть включена телеметрия или нет. Возможные `true` значения: `false` или (по умолчанию).| 


## <a name="next-steps"></a>Дальнейшие действия

- [Создание ресурса Insights приложений](../azure-monitor/app/create-new-resource.md)
- Узнайте, как [отслеживать поведение пользователей в Azure Active Directory B2C с помощью Application Insights](analytics-with-application-insights.md)
