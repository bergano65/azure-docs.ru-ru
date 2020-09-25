---
title: Примеры для JavaScript
titleSuffix: Azure AD B2C
description: Узнайте, как использовать JavaScript в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.custom: devx-track-js
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9faca55f8440a28a845e892ee38df2de3489f97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259498"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Примеры JavaScript для Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Вы можете добавить собственный код на стороне клиента JavaScript в приложения Azure Active Directory B2C (Azure AD B2C).

Чтобы включить JavaScript для приложений, выполните следующие действия.

* Добавление элемента в [настраиваемую политику](custom-policy-overview.md)
* Выберите [Макет страницы](page-layout.md)
* Использование [b2clogin.com](b2clogin.md) в запросах

В этой статье объясняется, как изменить настраиваемые политики, чтобы разрешить выполнение сценариев.

> [!NOTE]
> Если вы хотите включить JavaScript для потоков пользователей, см. раздел [версии JavaScript и страницы в Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Предварительные требования

### <a name="select-a-page-layout"></a>Выберите макет страницы

* Выберите [Макет страницы](contentdefinitions.md#select-a-page-layout) для элементов пользовательского интерфейса приложения.

    Если предполагается использовать JavaScript, необходимо [определить версию макета страницы](contentdefinitions.md#migrating-to-page-layout) с `contract` версией страницы для *всех* определений содержимого в пользовательской политике.

## <a name="add-the-scriptexecution-element"></a>Добавление элемента ScriptExecution

Включите выполнение сценариев. Для этого добавьте элемент **ScriptExecution** в элемент [RelyingParty](relyingparty.md).

1. Откройте файл настраиваемой политики. Например, *SignUpOrSignin.xml*.
2. Добавьте элемент **ScriptExecution** в элемент **UserJourneyBehaviors** в **RelyingParty**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Сохраните и отправьте этот файл.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

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

Замените `termsOfUseUrl` ссылкой на ваше соглашение об условиях использования. Для каталога создайте новый атрибут пользователя с именем **термсофусе** , а затем включите **термсофусе** в качестве атрибута пользователя.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о настройке пользовательского интерфейса приложений см. в статье [Настройка пользовательского интерфейса приложения с помощью настраиваемой политики в Azure Active Directory B2C](custom-policy-ui-customization.md).
