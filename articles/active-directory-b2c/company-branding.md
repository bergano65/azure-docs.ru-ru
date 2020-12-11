---
title: Добавление фирменной символики на страницу входа вашей организации
titleSuffix: Azure AD B2C
description: Узнайте, как добавить фирменную символику Организации на Azure Active Directory B2C страницы.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111498"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Добавление фирменной символики на страницы Azure Active Directory B2C Организации

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Вы можете настроить страницы Azure AD B2C с логотипом баннера, фоновым изображением и цветом фона с помощью Azure Active Directory [фирменной символики компании](../active-directory/fundamentals/customize-branding.md). Фирменная символика компании включает в себя регистрацию, вход, изменение профиля и сброс пароля. 

> [!TIP]
> Сведения о настройке других аспектов пользовательских страниц, которые выходят за пределы логотипа баннера, фонового изображения или цвета фона, см. в разделе [Настройка пользовательского интерфейса с помощью шаблона HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Чтобы настроить страницы потока пользователей, сначала настройте фирменную символику компании в Azure Active Directory, а затем включите ее в макетах страниц пользователя в Azure AD B2C.

## <a name="configure-company-branding"></a>Настройка фирменной символики компании

Начните с настройки логотипа баннера, фонового изображения и цвета фона в рамках **фирменной символики компании**.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. На портале Azure найдите и выберите **Azure AD B2C**.
1. В разделе **Управление** выберите **Корпоративная фирменная символика**.
1. Выполните действия, описанные в статье [Добавление фирменной символики на страницу входа Azure Active Directory вашей организации](../active-directory/fundamentals/customize-branding.md).

При настройке фирменной символики компании в Azure AD B2C необходимо учитывать следующие моменты.

* Фирменная символика компании в Azure AD B2C в настоящее время ограничена **фоновым изображением**, **логотипом баннера** и настройкой **цвета фона** . Другие свойства в области фирменной символики компании, например, в **дополнительных параметрах**, *не поддерживаются*.
* На страницах потока пользователя цвет фона отображается перед загрузкой фонового изображения. Мы рекомендуем выбрать цвет фона, который ближе всего соответствует цветам в фоновом изображении, чтобы обеспечить более гладкую загрузку.
* Эмблема баннера появляется в сообщениях электронной почты для проверки, отправляемых пользователям при инициировании потока регистрации пользователя.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Включение фирменной символики на страницах потока пользователя

После настройки фирменной символики компании включите ее в пользовательских потоках.

1. В меню слева портал Azure выберите **Azure AD B2C**.
1. В разделе **Политики** выберите **Пользовательские потоки (политики)** .
1. Выберите поток пользователя, для которого вы хотите включить фирменную символику компании. Фирменная символика компании **не поддерживается** для стандартных типов *входа* и *редактирования стандартного профиля* .
1. В разделе **Настройка** выберите **макеты страниц**, а затем выберите макет для фирменной символики. Например, выберите **Единая регистрация или страница входа**.
1. В качестве **версии макета страницы (Предварительная версия)** выберите версию **1.2.0** или более позднюю.
1. Щелкните **Сохранить**.

Если вы хотите настроить фирменную символику всех страниц в потоке пользователя, задайте версию макета страницы для каждого макета страницы в потоке пользователя.

![Выбор макета страницы в Azure AD B2C в портал Azure](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Выберите макет страницы

Чтобы включить фирменную символику компании, необходимо [определить версию макета страницы](contentdefinitions.md#migrating-to-page-layout) с версией страницы `contract` для *всех* определений содержимого в пользовательской политике. Формат значения должен содержать слово `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**контракт**:p Age-Name: версия_. Чтобы указать макет страницы в пользовательских политиках, использующих старое значение **DataUri** .

Сведения о [переходе на макет страницы](contentdefinitions.md#migrating-to-page-layout) с использованием версии страницы.

В следующем примере показаны идентификаторы определения содержимого и соответствующие **DataUri** с контрактом страницы: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

В следующем примере показан пользовательский логотип баннера и фоновое изображение на странице " *Регистрация и вход* пользователя", использующей синий шаблон "морской краски":

![Страница регистрации или входа с фирменной символикой, обслуживаемая Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Использование активов фирменной символики компании в пользовательском HTML

Чтобы использовать активы фирменной символики компании в [пользовательском HTML-коде](customize-ui-with-html.md), добавьте следующие теги за пределами `<div id="api">` тега:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Источник изображения заменяется на изображение фонового изображения и логотипа баннера.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о настройке пользовательского интерфейса приложения в [Azure Active Directory B2C](customize-ui-with-html.md)см. в этой статье.