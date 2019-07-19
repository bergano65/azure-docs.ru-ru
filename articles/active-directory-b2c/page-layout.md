---
title: Выберите макет страницы — Azure Active Directory B2C
description: Узнайте, как выбрать макет страницы в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 011fb262ff91c56269c5b7dc9adf4aaeab9acbd5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229054"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Выбор макета страницы в Azure Active Directory B2C с помощью настраиваемых политик

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Вы можете включить клиентский код JavaScript Azure Active Directory в политиках B2C (Azure AD), независимо от того, используете ли вы пользовательские потоки или пользовательские политики. Чтобы включить JavaScript для приложений, необходимо добавить элемент в [настраиваемую политику](active-directory-b2c-overview-custom.md), выбрать макет страницы и использовать [b2clogin.com](b2clogin.md) в запросах.

Макет страницы — это ассоциация элементов, которые Azure AD B2C предоставляет и предоставленное вами содержимое.

В этой статье описано, как выбрать макет страницы в Azure AD B2C, настроив его в пользовательской политике.

> [!NOTE]
> Если вы хотите включить JavaScript для потоков пользователей, см. раздел [версии JavaScript и страницы в Azure Active Directory B2C](user-flow-javascript-overview.md).

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

Чтобы выбрать макет страницы, измените значения **DataUri** в [ContentDefinitions](contentdefinitions.md) в политиках. Перейдя от старых значений **DataUri** к новым значениям, вы выбираете неизменяемый пакет. Преимущество использования этого пакета состоит в том, что он не изменится и не приведет к непредвиденной работе на странице.

Чтобы настроить макет страницы, используйте следующую таблицу для поиска значений **DataUri** .

| Старое значение DataUri | Новое значение DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Журнал изменений версий

Пакеты макета страницы периодически обновляются для включения исправлений и улучшений в элементы страницы. В следующем журнале изменений указаны изменения, появившиеся в каждой версии.

### <a name="110"></a>1.1.0

- Страница "исключения" (глобалексцептион)
  - Исправление специальных возможностей
  - Удалено сообщение по умолчанию, если в политике нет контакта
  - CSS по умолчанию удален
- Страница MFA (многофакторная)
  - Кнопка "подтвердить код" удалена
  - Поле ввода для кода теперь принимает только до шести (6) символов.
  - Страница будет автоматически пытаться проверить код, указанный при входе в 6-значный код, без нажатия кнопки
  - Если код неправильный, поле ввода автоматически снимается
  - После трех (3) попыток с неверным кодом B2C отправляет ошибку проверяющей стороне.
  - Исправления специальных возможностей
  - CSS по умолчанию удален
- Страница с автоматическим подтверждением (selfasserted)
  - Удалено оповещение об отмене
  - Класс CSS для элементов error
  - Улучшена функция отображения или скрытия логики ошибок
  - CSS по умолчанию удален
- Унифицированный поставщик общих служб (унифиедссп)
  - Добавлен элемент управления "оставаться в системе" (функции "оставаться)

### <a name="100"></a>1.0.0

- Первый выпуск

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о настройке пользовательского интерфейса приложений см. в статье [Настройка пользовательского интерфейса приложения с помощью настраиваемой политики в Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
