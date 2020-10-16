---
title: Настройка языка в потоках пользователей Azure AD
description: Дополнительные сведения о настройке языка в потоках пользователя.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93b2b5538df894e17449e173baa3e402d6c59654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91354995"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Настройка языка в Azure Active Directory (предварительная версия)

> [!NOTE]
> Функция самостоятельной регистрации в Azure Active Directory предоставляется в режиме общедоступной предварительной версии. См. подробные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Настройка языка в Azure Active Directory (Azure AD) позволяет добавить в поток пользователя различные языки в соответствии с потребностями ваших пользователей. Корпорация Майкрософт предоставляет переводы для [36 языков](#supported-languages). Даже если интерфейс предоставляется только для одного языка, вы можете настроить имена атрибутов на странице "Коллекция атрибутов".

## <a name="how-language-customization-works"></a>Как работает функция настройки языка

По умолчанию настройка языка включена для пользователей, которые регистрируются, чтобы предоставить согласованное взаимодействие при регистрации. Вы можете использовать языки для изменения строк, отображаемых пользователям в процессе сбора атрибутов во время регистрации.

> [!NOTE]
> Если используются пользовательские атрибуты, необходимо предоставить собственный перевод. Дополнительные сведения см. в разделе [Настройка строк](#customize-your-strings).

## <a name="customize-your-strings"></a>Настройка строк

Настройка языка позволяет настроить любую строку в информации, передаваемой потоку пользователя.

1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора Azure AD.
2. В разделе **Службы Azure** щелкните **Azure Active Directory**.
3. В меню слева щелкните **Внешние удостоверения**.
4. Выберите **Потоки пользователей (предварительная версия)** .
3. Выберите поток пользователя, который необходимо включить для переводов.
4. Щелкните **Языки**.
5. На странице **Языки** для потока пользователя выберите язык, который требуется настроить.
6. Разверните **страницу сбора атрибутов**
7. Нажмите кнопку **Скачать значения по умолчанию** (или **Скачать переопределения**, если этот язык ранее менялся).

В результате будет создан JSON-файл, который можно использовать для изменения строк.

### <a name="change-any-string-on-the-page"></a>Замена любой строки на странице

1. Откройте скачанный JSON-файл в редакторе JSON.
1. Найдите элемент, требующий замены. Вы можете найти `StringId` искомой строки или найти атрибут `Value`, который вы хотите изменить.
1. Замените атрибут `Value` значением, которое требуется отобразить.
1. В каждой строке, которую необходимо изменить, замените `Override` на `true`.
1. Сохраните файл и отправьте изменения. (Элемент управления отправкой можно найти в том же месте, где вы загружали JSON-файл.)

> [!IMPORTANT]
> Если необходимо переопределить строку, измените значение `Override` на `true`. В противном случае запись не учитывается.

### <a name="change-extension-attributes"></a>Изменение атрибутов расширения

Если вы хотите изменить или добавить в JSON строку пользовательского атрибута, это нужно сделать в следующем формате:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Замените `<ExtensionAttribute>` именем пользовательского атрибута.

Замените `<ExtensionAttributeValue>` новой строкой для отображения.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Укажите список значений с помощью LocalizedCollections

Если вы хотите предоставить список значений для ответов, необходимо создать атрибут `LocalizedCollections`. `LocalizedCollections` является массивом пар `Name` и `Value`. Порядок размещения элементов определяет порядок их отображения. Для добавления `LocalizedCollections` используйте следующий формат.

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` представляет собой пользовательский атрибут, ответом на который является `LocalizedCollections`.
* `Name` является значением, которое видит пользователь.
* `Value` возвращается в утверждении при выборе этого параметра.

### <a name="upload-your-changes"></a>Загрузка изменений

1. Внеся изменения в JSON-файл, вернитесь к клиенту.
1. Щелкните **Потоки пользователей** и выберите поток пользователя, который необходимо включить для переводов.
1. Щелкните **Языки**.
1. Выберите язык, на который требуется выполнить перевод.
1. Выберите **страницу сбора атрибутов**
1. Щелкните значок папки и выберите JSON-файл для отправки.

Изменения автоматически сохраняются в потоке пользователя.

## <a name="additional-information"></a>Дополнительные сведения

### <a name="page-ui-customization-labels-as-overrides"></a>Использование меток настройки пользовательского интерфейса страницы для переопределения параметров страницы

При включении настройки языка все предыдущие изменения меток, использующих настройки пользовательского интерфейса страницы, сохраняются в JSON-файле для английского языка (en). Вы можете продолжить изменять метки и другие текстовые строки, отправив языковые ресурсы в настройках языка.

### <a name="up-to-date-translations"></a>Актуальные переводы

Корпорация Майкрософт стремится предоставить наиболее актуальные переводы Мы постоянно улучшаем переводы в соответствии с вашими требованиями. Мы выявляем ошибки и изменения в глобальной терминологии и обновляем ее, обеспечивая непрерывную поддержку в потоке пользователя.

### <a name="support-for-right-to-left-languages"></a>Поддержка языков с написанием справа налево

Корпорация Майкрософт сейчас не поддерживает языки с написанием справа налево. Их поддержку можно реализовать, используя пользовательские языковые стандарты и каскадные таблицы стилей для изменения способа отображения строк. При необходимости такой поддержки проголосуйте на [этом форуме для пользователей Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Переводы для поставщиков удостоверений в социальных сетях

Корпорация Майкрософт предоставляет параметр `ui_locales` OIDC для имен входа в социальные сети. Однако некоторые поставщики удостоверений в социальных сетях, включая Facebook и Google, не принимают его.

### <a name="browser-behavior"></a>Поведение браузера

И Chrome, и Firefox запрашивают настроенный язык. Если этот язык поддерживается, ему отдается предпочтение над языком, заданным по умолчанию. Сейчас Microsoft Edge не запрашивает язык и сразу отображает язык по умолчанию.

## <a name="supported-languages"></a>Поддерживаемые языки

Azure AD поддерживает следующие языки. Языки потока пользователей предоставляются Azure AD. Языки уведомлений многофакторной идентификации (MFA) предоставляются [Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

| Язык              | Код языка | Маршруты пользователей         | Уведомления MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Арабский                | ar            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Болгарский             | bg            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Бенгальский                | bn            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Каталонский               | ca            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Чешский                 | cs            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Датский                | da            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Немецкий                | de            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Греческий                 | el            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Английский               | en            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Испанский               | es            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Эстонский              | et            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Баскский                | eu            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Финский               | fi            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Французский                | fr            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Галисийский              | gl            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Гуджарати              | gu            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Иврит                | he            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Hindi                 | hi            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Хорватский              | hr            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Венгерский             | hu            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Индонезийский            | идентификатор            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Итальянский               | it            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Японский              | ja            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Казахский                | kk            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Каннада               | kn            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Корейский                | ko            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Литовский            | lt            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Латышский               | lv            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Малаялам             | ml            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Маратхи               | mr            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Малайский                 | ms            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Норвежский (букмол)      | nb            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Нидерландский                 | nl            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Норвежский             | нет            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Панджаби               | pa            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Польский                | pl            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Португальский (Бразилия)   | pt-br         | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Португальский (Португалия) | pt-pt         | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Румынский              | ro            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Русский               | ru            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Словацкий                | sk            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Словенский             | sl            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Сербский — кириллица    | sr-cryl-cs    | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Сербский — латиница       | sr-latn-cs    | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Шведский               | sv            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Тамильский                 | ta            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Телугу                | te            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) |
| Тайский                  | th            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Турецкий               | tr            | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Украинский             | uk            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Вьетнамский            | vi            | ![X, указывающее на отсутствие.](./media/user-flow-customize-language/no.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Китайский (упрощенное письмо)  | zh-hans       | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |
| Китайский (традиционное письмо) | zh-hant       | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) | ![Зеленая галочка.](./media/user-flow-customize-language/yes.png) |