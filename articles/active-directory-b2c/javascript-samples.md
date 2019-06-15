---
title: Примеры JavaScript — Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как использовать JavaScript в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9e19df7c50ca9d2c57ab385a567f4911b200c5e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510890"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Примеры JavaScript для Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

К приложениям Azure Active Directory (Azure AD) B2C можно добавить собственный код JavaScript на стороне клиента. Чтобы включить JavaScript для приложений, необходимо добавить элемент к вашей [настраиваемой политики](active-directory-b2c-overview-custom.md)выберите [контракта страницы](page-contract.md)и использовать [b2clogin.com](b2clogin.md) в запросах. Этой статье описывается, как можно изменить пользовательских политик для разрешения выполнения сценариев.

> [!NOTE]
> Если вы хотите включить JavaScript для пользователя потоков, см. в разделе [JavaScript и странице контракта версии в Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Технические условия

Выберите контракт страница для элементов пользовательского интерфейса приложения. Если вы планируете использовать JavaScript, нужно определить версию контракта страницы для всех определений содержимого в настраиваемой политике.

## <a name="add-the-scriptexecution-element"></a>Добавление элемента ScriptExecution

Включите выполнение сценариев. Для этого добавьте элемент **ScriptExecution** в элемент [RelyingParty](relyingparty.md).

1. Откройте файл настраиваемой политики. Например, *SignUpOrSignin.xml*.
2. Добавьте элемент **ScriptExecution** в элемент **UserJourneyBehaviors** в **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Сохраните и отправьте этот файл.

## <a name="guidelines-for-using-javascript"></a>Рекомендации по использованию JavaScript

При настройке интерфейса приложения с помощью JavaScript следуйте приведенным ниже рекомендациям.

- Не привязывайте событие click к элементам HTML `<a>`.
- Не принимайте зависимость от кода Azure AD B2C или комментариев.
- Не изменяйте порядок или иерархию элементов HTML в Azure AD B2C. Управляйте порядком элементов пользовательского интерфейса с помощью политики Azure AD B2C.
- С учетом приведенных ниже рекомендаций можно вызвать любую службу RESTful:
    - Возможно, потребуется установить CORS службы RESTful, чтобы разрешить вызовы HTTP на стороне клиента.
    - Убедитесь, что служба RESTful безопасна и использует только протокол HTTPS.
    - Не используйте JavaScript непосредственно для вызова конечных точек Azure AD B2C.
- Вы можете внедрить JavaScript или привязать внешние файлы JavaScript. Если вы привязываете внешний файл JavaScript, обязательно используйте абсолютный URL-адрес, а не относительный URL-адрес.
- Платформы JavaScript:
    - Azure AD B2C использует конкретную версию jQuery. Не включайте другие версии jQuery. Использование более одной версии на одной и той же странице вызывает проблемы.
    - RequireJS не поддерживается.
    - Azure AD B2C не поддерживает большинство платформ JavaScript.
- Параметры Azure AD B2C можно считывать с помощью вызова объектов `window.SETTINGS`, `window.CONTENT`, таких как текущий язык пользовательского интерфейса. Не изменяйте значения этих объектов.
- Чтобы настроить сообщение об ошибке Azure AD B2C, используйте локализацию в политике.
- Если что-то можно реализовать с помощью политики, рекомендуем использовать именно этот способ.

## <a name="javascript-samples"></a>Примеры для JavaScript

### <a name="show-or-hide-a-password"></a>Отображение или скрытие пароля

Распространенный способ помочь клиентам при регистрации — позволить им просмотреть пароль, который они ввели. Благодаря этой функции при необходимости они могут легко вносить изменения в пароль. В каждом поле ввода пароля есть флажок с подписью **Показать пароль**.  Если установить его, пароль отобразится в виде обычного текста. Включите этот фрагмент кода в шаблон для страницы подтверждения регистрации или входа в систему:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Добавление условий использования

Включите приведенный ниже код в страницу, где требуется отобразить флажок **Условия использования**. Этот флажок обычно необходим на страницах регистрации локальной учетной записи и на страницах регистрации учетной записи социальных сетей.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Замените `termsOfUseUrl` ссылкой на ваше соглашение об условиях использования. Для каталога, создайте новый атрибут пользователя **termsOfUse** и включите **termsOfUse** как атрибут пользователя.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о настройке пользовательского интерфейса приложений см. в статье [Настройка пользовательского интерфейса приложения с помощью настраиваемой политики в Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
