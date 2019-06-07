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
ms.openlocfilehash: f3621b176e4bbfdfbd171339d6d01a1f91ed0ae7
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509288"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Управление единым входом и настройкой токенов с помощью пользовательских политик в Azure Active Directory B2C

Эта статья содержит сведения об управлении токенами, сеансами и настройками единого входа (SSO) с помощью [Пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Конфигурация времени существования токенов и утверждений

Чтобы изменить настройки времени существования токенов, добавьте элемент [ClaimsProviders](claimsproviders.md) в файл проверяющей стороны политики, которую следует изменить.  Элемент **ClaimsProviders** является дочерним элементом [TrustFrameworkPolicy](trustframeworkpolicy.md). 

Вставьте элемент ClaimsProviders между элементами BasePolicy и RelyingParty доверяющей стороной файла.

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
- **Время существования сеанса веб-приложения (в минутах).** Время существования сеанса веб-приложения устанавливается с помощью элемента **SessionExpiryInSeconds**. Значение по умолчанию составляет 86400 секунд (1440 минут).
- **Время ожидания сеанса веб-приложения.** Время ожидания сеанса веб-приложения устанавливается с помощью элемента **SessionExpiryType**. Применимые значения — `Absolute` и `Rolling`.
