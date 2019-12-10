---
title: Определение технического профиля преобразования утверждений
titleSuffix: Azure AD B2C
description: Определите технический профиль преобразования утверждений в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bf06fe7d4e529eb04b156a2d61011198a6fe0978
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949429"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля преобразования утверждений в пользовательской политике в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Технический профиль преобразования утверждений позволяет вызывать исходящие преобразования утверждений для обработки значений утверждений, проверки утверждений или установки значений по умолчанию для набора исходящих утверждений.

## <a name="protocol"></a>Протокол

Для атрибута **Name** элемента **Protocol** необходимо задать значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, которое используется Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

В следующем примере показан технический профиль для преобразования утверждений:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** является обязательным. Необходимо указать по крайней мере одно исходящее утверждение, возвращаемое техническим профилем. В следующем примере показано, как установить значения по умолчанию в исходящих утверждениях:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Преобразования исходящих утверждений

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения или создания утверждений. Следующий технический профиль вызывает преобразование утверждений **RemoveAlternativeSecurityIdByIdentityProvider**. Это преобразование утверждений удаляет удостоверение социальной сети из коллекции **AlternativeSecurityIds**. Исходящие утверждения этого технического профиля — **identityProvider2**, которому задано значение `facebook.com`, и **AlternativeSecurityIds**, который содержит список удостоверений социальных сетей, связанный с данным пользователем, после удаления удостоверения facebook.com.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Технический профиль преобразования утверждений позволяет выполнять преобразование утверждений на любом шаге оркестрации на пути взаимодействия пользователя. В следующем примере шаг оркестрации вызывает один из технических профилей с удаленной связью, такой как **UnLink-Facebook-OAUTH**. Этот технический профиль вызывает технический профиль преобразования утверждений **RemoveAlternativeSecurityIdByIdentityProvider**, который создает утверждение **AlternativeSecurityIds2**, содержащее список пользовательских удостоверений социальных сетей при удалении удостоверения Facebook из коллекций.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="use-a-validation-technical-profile"></a>Использование технического профиля проверки

Технический профиль преобразования утверждений может использоваться для проверки информации. В следующем примере [самоподтвержденный технический профиль](self-asserted-technical-profile.md) с именем **LocalAccountSignUpWithLogonEmail** предлагает пользователю дважды ввести адрес электронной почты, а затем вызывает [технический профиль проверки](validation-technical-profile.md) с именем **Validate-Email** для проверки адреса электронной почты. Технический профиль **Validate-Email** вызывает преобразование утверждений **AssertEmailAreEqual** для сравнения двух утверждений **email** и **emailRepeat**. Если они не равны в соответствии с указанным сравнением, он создает исключение.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Технический профиль преобразования утверждений вызывает преобразование утверждений **AssertEmailAreEqual**, которое подтверждает, что адреса электронной почты, предоставленные пользователем, одинаковы.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Самоподтвержденный технический профиль может вызвать технический профиль проверки и отобразить сообщение об ошибке, указанное в метаданных **UserMessageIfClaimsTransformationStringsAreNotEqual**.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
