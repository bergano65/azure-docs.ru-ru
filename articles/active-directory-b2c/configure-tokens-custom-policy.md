---
title: Управление единым входом и настройкой маркеров с помощью пользовательских политик
titleSuffix: Azure AD B2C
description: Сведения об управлении единым входом и настройкой токенов с помощью пользовательских политик в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0b460d5a3c5535b74e349fa46c6a2ad55fc3a8d8
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966575"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Управление единым входом и настройкой токенов с помощью пользовательских политик в Azure Active Directory B2C

В этой статье содержатся сведения об управлении конфигурациями маркеров, сеансов и единого входа (SSO) с помощью [пользовательских политик](custom-policy-overview.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="jtw-token-lifetimes-and-claims-configuration"></a>Время существования токена JTW и конфигурация утверждений

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

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [Azure AD B2C сеансе](session-overview.md).
- Узнайте, как [настроить поведение сеанса в пользовательских политиках](session-behavior-custom-policy.md).
- Ссылка: [жвтиссуер](jwt-issuer-technical-profile.md).