---
title: Настройка языка в Azure Active Directory B2C
description: Дополнительные сведения о настройке языка в потоках пользователя.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd6e22052d2fab6d39382b21eec7a7bd379adb82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384079"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Настройка языка в Azure Active Directory B2C

Настройка языка в Azure Active Directory B2C (Azure AD B2C) позволяет добавить в поток пользователя различные языки в соответствии с потребностями ваших клиентов. Корпорация Майкрософт предоставляет переводы на [36 языках](#supported-languages). Кроме того, вы можете предоставить собственный перевод на любой язык. Даже если вам требуется только один язык, на странице можно настроить любой текст.

## <a name="how-language-customization-works"></a>Как работает функция настройки языка

Настройка языка позволяет выбрать языки, на которых доступен поток пользователя. Если эта функция включена, вы можете указать параметр строки запроса `ui_locales` в приложении. При вызове Azure AD B2C выполняется перевод страницы на указанный языковой стандарт. Такой тип конфигурации предоставляет вам полный контроль над языками, доступными потоку пользователя, а языковые параметры браузера клиента игнорируются.

Иногда вам может не понадобиться такой уровень контроля за тем, какие языки будут доступны пользователю. Если не указать параметр `ui_locales`, настройка языка будет зависеть от параметров браузера. Вы сможете управлять тем, какие языки будут доступны потоку пользователя, добавив их в качестве поддерживаемых. Если браузер клиента настроен для отображения на языке, который не требуется поддерживать, вместо него будет отображаться язык, выбранный по умолчанию для поддерживаемых языков.

* **язык, указанный в пользовательском интерфейсе**: после включения настройки языка поток пользователя будет переведен на указанный здесь язык.
* **Язык, запрошенный браузером**. Если `ui_locales` параметр не указан, поток пользователя преобразуется в требуемый браузером язык, *Если язык поддерживается*.
* **Язык политики по умолчанию**: Если в браузере не указан язык или он указывает, что он не поддерживается, поток пользователя преобразуется в язык пользовательского потока по умолчанию.

> [!NOTE]
> Если используются пользовательские атрибуты, необходимо предоставить собственный перевод. Дополнительные сведения см. в разделе [Настройка строк](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Поддержка запрашиваемых языков для ui_locales

Сначала необходимо включить функцию настройки языка для политик, которые были созданы до выхода общедоступного выпуска этой функции. Для политик и потоков пользователей, созданных позже, настройка языка включена по умолчанию.

Включив в потоке пользователя настройку языка, вы сможете управлять языками, доступными потоку пользователя, добавив параметр `ui_locales`.

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните поток пользователя, который необходимо включить для переводов.
1. Щелкните **Языки**.
1. Выберите **Включение настройки языка**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Выбор языков для включения в потоке пользователя

Включите набор языков для потока пользователя, на которые будет выполняться перевод по запросу браузера без параметра `ui_locales`.

1. Убедитесь, что в потоке пользователя включена настройка языка, как описано выше.
1. На странице **Языки** для потока пользователя выберите язык, который требуется поддерживать.
1. На панели свойств переведите переключатель **Включено** в положение **Да**.
1. Выберите **Сохранить** в верхней части панели свойств.

>[!NOTE]
>Если параметр `ui_locales` не указан, страница будет переведена на язык клиентского браузера только в случае включения этой функции.
>

## <a name="customize-your-strings"></a>Настройка строк

Настройка языка позволяет настроить любую строку в информации, передаваемой потоку пользователя.

1. Убедитесь, что в потоке пользователя включена настройка языка, как описано выше.
1. На странице **Языки** для потока пользователя выберите язык, который требуется настроить.
1. В разделе **Page-level-resources files** (Файлы ресурсов на уровне страницы) выберите страницу, которую требуется изменить.
1. Нажмите кнопку **Скачать значения по умолчанию** (или **Скачать переопределения**, если этот язык ранее менялся).

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

```json
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

```json
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

1. Внеся изменения в JSON-файл, вернитесь к клиенту B2C.
1. Щелкните **Потоки пользователей** и выберите поток пользователя, который необходимо включить для переводов.
1. Щелкните **Языки**.
1. Выберите язык, на который требуется выполнить перевод.
1. Выберите страницу, которую необходимо перевести.
1. Щелкните значок папки и выберите JSON-файл для отправки.

Изменения автоматически сохраняются в потоке пользователя.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Настройка пользовательского интерфейса страницы с помощью настройки языка

Локализация HTML-содержимого выполняется двумя способами. Одним из способов является включение [настройка языка](user-flow-language-customization.md). Включение этой функции позволяет Azure AD B2C пересылать параметр OpenID Connect Connect `ui-locales` в конечную точку. Сервер содержимого может использовать этот параметр для предоставления настроенных страниц HTML на конкретном языке.

Кроме того, можно извлечь содержимое из разных расположений, в зависимости от используемого языкового стандарта. В конечной точке с поддержкой CORS можно настроить структуру папок для размещения содержимого для определенных языков. При использовании шаблона подстановки `{Culture:RFC5646}` запрос будет правильным. Например, предположим, что это URI вашей настраиваемой страницы.

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Страницу можно загрузить на `fr`. Когда страница подгружает содержимое HTML и CSS, она запрашивает его из:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Добавление пользовательских языков

Вы также можете добавить языки, на которые корпорация Майкрософт в настоящее время не предоставляет переводы. Необходимо будет предоставить переводы для всех строк в потоке пользователя. Язык и коды языковых стандартов ограничиваются предусмотренными в стандарте ISO 639-1.

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
2. Щелкните поток пользователя, в котором необходимо добавить пользовательские языки, и выберите **Языки**.
3. В верхней части страницы выберите **Добавить пользовательский язык**.
4. В открывшейся области контекста определите язык, для которого предоставляется перевод, введя код допустимого языкового стандарта.
5. Для каждой страницы можно загрузить набор переопределений для английского языка и начать работу над переводами.
6. По завершении операций с JSON-файлами их можно загрузить для каждой страницы.
7. Выберите **Включить**, после чего поток пользователя сможет отображать этот язык для пользователей.
8. Сохраните язык.

>[!IMPORTANT]
>Прежде чем вы сможете сохранить, необходимо включить пользовательские языки или отправить переопределения для них.

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

Azure AD B2C включает поддержку следующих языков. Языки потока пользователей предоставляются Azure AD B2C. Языки уведомлений многофакторной идентификации (MFA) предоставляются [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Язык              | Код языка | Маршруты пользователей         | Уведомления MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Арабский                | ar            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Болгарский             | bg            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Бенгальский                | bn            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Каталонский               | ca            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Чешский                 | cs            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Датский                | da            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Немецкий                | de            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Греческий                 | el            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Английский               | en            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Испанский               | es            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Эстонский              | et            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Баскский                | eu            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Финский               | fi            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Французский                | fr            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Галисийский              | gl            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Гуджарати              | gu            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Иврит                | he            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Хорватский              | hr            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Венгерский             | hu            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Индонезийский            | идентификатор            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Итальянский               | it            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Японский              | ja            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Казахский                | kk            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Каннада               | kn            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Корейский                | ko            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Литовский            | lt            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Латышский               | lv            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Малаялам             | ml            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Маратхи               | mr            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Малайский                 | ms            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Норвежский (букмол)      | nb            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Нидерландский                 | nl            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Норвежский             | Нет            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Панджаби               | pa            | ![да](./media/user-flow-language-customization/yes.png) | ![нет](./media/user-flow-language-customization/no.png) |
| Польский                | pl            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Португальский (Бразилия)   | pt-br         | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Португальский (Португалия) | pt-pt         | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Румынский              | ro            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Русский               | ru            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Словацкий                | sk            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Словенский             | sl            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Сербский — кириллица    | sr-cryl-cs    | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Сербский — латиница       | sr-latn-cs    | ![нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Шведский               | sv            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Тамильский                 | ta            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Телугу                | te            | ![да](./media/user-flow-language-customization/yes.png) | ![Нет](./media/user-flow-language-customization/no.png) |
| Тайский                  | th            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Турецкий               | tr            | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Украинский             | uk            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Вьетнамский            | vi            | ![Нет](./media/user-flow-language-customization/no.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Китайский (упрощенное письмо)  | zh-hans       | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
| Китайский (традиционное письмо) | zh-hant       | ![да](./media/user-flow-language-customization/yes.png) | ![да](./media/user-flow-language-customization/yes.png) |
