---
title: Выберите контракт страницы — Azure Active Directory B2C | Документация Майкрософт
description: Дополнительные сведения о выборе контракта страницы в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4cd29df19179f07fd9b61a2f484b1d49cc05c4cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570578"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Выбор контракта страницы в Azure Active Directory B2C с помощью настраиваемых политик

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Вы можете включить код JavaScript на стороне клиента в политиках Azure Active Directory (Azure AD) B2C, независимо от того, используете ли вы потоки или настаиваемые политики пользователя. Чтобы включить JavaScript для приложений, необходимо добавить элемент к вашей [настраиваемой политики](active-directory-b2c-overview-custom.md), выберите контракт страницы и использовать [b2clogin.com](b2clogin.md) в запросах. Контракт страницы — это ассоциация элементов, предоставляемых Azure AD B2C, и содержимое, предоставляемое вами. В этой статье описывается выберите контракт страницы в Azure AD B2C, настроив его в настраиваемой политике.

> [!NOTE]
> Если вы хотите включить JavaScript для пользователя потоков, см. в разделе [JavaScript и странице контракта версии в Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Замена значений DataUri

В настраиваемых политиках могут быть элементы [ContentDefinitions](contentdefinitions.md), которые определяют шаблоны HTML, используемые в пути взаимодействия пользователя. **ContentDefinition** содержит **DataUri**, ссылающийся на элементы страницы, предоставляемые Azure AD B2C. **LoadUri** — это относительный путь к содержимому HTML и CSS, которое вы предоставляете.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Чтобы выбрать контракт страницы, необходимо изменить значения **DataUri** в элементах [ContentDefinitions](contentdefinitions.md) в ваших политиках. Перейдя от старых значений **DataUri** к новым значениям, вы выбираете неизменяемый пакет. Преимущество использования этого пакета состоит в том, что он не изменится и не приведет к непредвиденной работе на странице. 

Чтобы настроить контракт страницы, найдите значения **DataUri** в следующей таблице. 

| Старое значение DataUri | Новое значение DataUri |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о настройке пользовательского интерфейса приложений см. в статье [Настройка пользовательского интерфейса приложения с помощью настраиваемой политики в Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



