---
title: Определение Application Insights технического профиля в настраиваемой политике
titleSuffix: Azure AD B2C
description: Определите Application Insights технический профиль в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201418"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Определение Application Insights технического профиля в Azure AD B2C настраиваемой политике

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) поддерживает отправку данных событий непосредственно в [Application Insights](../azure-monitor/app/app-insights-overview.md) с помощью ключа инструментирования, предоставленного для Azure AD B2C.  С помощью Application Insights технического профиля можно получить подробные и настраиваемые журналы событий для пути взаимодействия пользователя:

* Получить сведения о поведении пользователей.
* Устранить проблемы в собственных политиках в среде разработки или в рабочей среде.
* Измерить производительность.
* Создать уведомления из Application Insights.


## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, используемой Azure AD B2C для Application Insights: `Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

В следующем примере показан стандартный технический профиль Application Insights. Другие Application Insights технические профили включают AzureInsights-Common, чтобы использовать его конфигурацию.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Входящие утверждения

Элемент **inputclaim** содержит список утверждений для отправки в Application Insights. Вы также можете сопоставлять имя заявки с именем, которое вы предпочитаете отображать в Application Insights. В следующем примере показано, как отправить телеметрии в Application Insights. Свойства события добавляются с помощью синтаксиса `{property:NAME}` , где name — это свойство, добавляемое к событию. DefaultValue может быть либо статическим значением, либо значением, которое разрешается одним из поддерживаемых [арбитров утверждений](claim-resolver-overview.md).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Элемент **инпутклаимстрансформатионс** может содержать коллекцию элементов **инпутклаимстрансформатион** , которые используются для изменения входных утверждений или для создания новых перед отправкой в Application Insights.

## <a name="persist-claims"></a>Сохранение утверждений

Элемент Персистедклаимс не используется.

## <a name="output-claims"></a>Исходящие утверждения

Элементы OutputClaims и Аутпутклаимстрансформатионс не используются.

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент CryptographicKeys не используется.


## <a name="metadata"></a>Метаданные

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| InstrumentationKey| Да | Application Insights [ключ инструментирования](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key), который будет использоваться для записи событий в журнал. | 
| DeveloperMode| Нет | Логическое значение, указывающее, включен ли режим разработчика. Возможные значения: `true` или `false` (по умолчанию). Эти метаданные определяют, как отправляются события в буфер. В среде разработки с минимальным объемом событий включение режима разработчика приводит к тому, что события немедленно отправляются на Application Insights.|  
|дисаблетелеметри |Нет |Логическое значение, указывающее, следует ли включить телеметрию. Возможные значения: `true` или `false` (по умолчанию).| 


## <a name="next-steps"></a>Дальнейшие действия

- [Создание ресурса Application Insights](../azure-monitor/app/create-new-resource.md)
- Сведения об [отслеживании поведения пользователей в Azure Active Directory B2C с помощью Application Insights](analytics-with-application-insights.md)
