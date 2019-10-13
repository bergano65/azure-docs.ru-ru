---
title: Управление единым входом и настройкой токенов с помощью пользовательских политик в Azure Active Directory B2C | Документация Майкрософт
description: Сведения об управлении единым входом и настройкой токенов с помощью пользовательских политик в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 36a95b502c13ccf360ba4ac56b4837d41ee487c8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296410"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Управление единым входом и настройкой токенов с помощью пользовательских политик в Azure Active Directory B2C

В этой статье содержатся сведения об управлении конфигурациями маркеров, сеансов и единого входа (SSO) с помощью [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Конфигурация времени существования токенов и утверждений

Чтобы изменить настройки времени существования токенов, добавьте элемент [ClaimsProviders](claimsproviders.md) в файл проверяющей стороны политики, которую следует изменить.  Элемент **ClaimsProviders** является дочерним элементом [TrustFrameworkPolicy](trustframeworkpolicy.md).

Вставьте элемент Клаимспровидерс между элементом Басеполици и элементом Релингпарти файла проверяющей стороны.

Внутри необходимо разместить сведения, влияющие на время существования токенов. Пример XML выглядит так:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

В предыдущем примере установлены следующие значения.

- **Время существования маркеров доступа.** Время существования маркеров доступа устанавливается с помощью элемента метаданных **token_lifetime_secs**. Значение по умолчанию составляет 3600 секунд (60 минут).
- **Время существования токена идентификатора.** Время существования токена идентификатора устанавливается с помощью элемента метаданных **id_token_lifetime_secs**. Значение по умолчанию составляет 3600 секунд (60 минут).
- **Время существования токена обновления.** Время существования токена обновления устанавливается с помощью элемента метаданных **refresh_token_lifetime_secs**. Значение по умолчанию составляет 1209600 секунд (14 дней).
- **Время существования скользящего окна токена обновления.** Если вы хотите настроить время существования скользящего окна для токена обновления, установите значение **rolling_refresh_token_lifetime_secs** элемента метаданных. Значение по умолчанию — 7776000 (90 дней). Если вы не хотите задавать определенное время существования скользящего окна, замените этот элемент на `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Утверждение издателя (iss).** Утверждение издателя (iss) устанавливается с помощью элемента метаданных **IssuanceClaimPattern**. Применимые значения — `AuthorityAndTenantGuid` и `AuthorityWithTfp`.
- **Утверждение настройки, представляющее идентификатор политики.** Параметры, использующиеся для настройки этого значения, — `TFP` (инфраструктура доверия) и `ACR` (ссылка на контекст проверки подлинности). `TFP` является рекомендуемым значением. Установите значение `None` для **AuthenticationContextReferenceClaimPattern**.

    В элемент **ClaimsSchema** добавьте такой элемент.

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Добавьте следующий элемент в элемент **OutputClaims**.

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Чтобы использовать ACR, удалите элемент **AuthenticationContextReferenceClaimPattern**.

- **Утверждение субъекта (sub).** По умолчанию для этого параметра задано значение ObjectID. Если вы хотите установить значение `Not Supported`, замените эту строку:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    на следующую:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Режим работы сеанса и SSO

Чтобы изменить конфигурации режима работы сеанса и SSO, добавьте элемент **UserJourneyBehaviors** в элемент [RelyingParty](relyingparty.md).  Элемент **UserJourneyBehaviors** должен сразу же следовать за элементом **DefaultUserJourney**. Содержимое элемента **UserJourneyBehavors** должно выглядеть следующим образом.

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

В предыдущем примере установлены следующие значения.

- **Включение единого входа (SSO).** Включение единого входа (SSO) настроено с помощью **SingleSignOn**. Применимые значения — `Tenant`, `Application`, `Policy` и `Suppressed`.
- **Время ожидания сеанса веб-приложения.** Время ожидания сеанса веб-приложения устанавливается с помощью элемента **SessionExpiryType**. Применимые значения — `Absolute` и `Rolling`.
- **Время существования сеанса веб-приложения** . время существования сеанса веб-приложения задается с помощью элемента **сессионекспиринсекондс** . Значение по умолчанию составляет 86400 секунд (1440 минут).
