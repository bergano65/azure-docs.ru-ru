---
title: Справочник по пакету SDK для иммерсивного чтения
titleSuffix: Azure Cognitive Services
description: Пакет SDK для иммерсивное средство чтения содержит библиотеку JavaScript, которая позволяет интегрировать иммерсивное средство чтения в приложение.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 879834567b6905a070aada3dae2a41a672635c6c
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267245"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Справочное руководство по пакету SDK для иммерсивного чтения

Пакет SDK для иммерсивное средство чтения содержит библиотеку JavaScript, которая позволяет интегрировать иммерсивное средство чтения в приложение.

## <a name="functions"></a>Функции

Пакет SDK предоставляет функции:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>лаунчасинк

Запускает иммерсивное средство чтения в `iframe` веб-приложении.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Параметры

| Имя | Тип | Описание |
| ---- | ---- |------------ |
| `token` | строка | Маркер проверки подлинности Azure AD. |
| `subdomain` | строка | Пользовательский поддомен для иммерсивного ресурса чтения в Azure. |
| `content` | [Содержимое](#content) | Объект, содержащий содержимое, которое должно отображаться в иммерсивное средство чтения. |
| `options` | [Параметры](#options) | Параметры для настройки определенного поведения иммерсивное средство чтения. Необязательный элемент. |

### <a name="returns"></a>Результаты

Возвращает объект `Promise<LaunchResponse>` , который разрешается при загрузке иммерсивное средство чтения. `Promise`Разрешается в [`LaunchResponse`](#launchresponse) объект.

### <a name="exceptions"></a>Исключения

Возвращенный `Promise` объект будет отклонен с [`Error`](#error) объектом, если не удается загрузить иммерсивное средство чтения. Дополнительные сведения см. в [кодах ошибок](#error-codes).

## <a name="close"></a>close

Закрывает иммерсивное средство чтения.

Примером использования этой функции является случай, если кнопка выход скрыта с помощью параметра ```hideExitButton: true``` в [параметрах](#options). Затем с помощью другой кнопки (например, стрелки обратного заголовка мобильного устройства) можно вызвать эту ```close``` функцию при ее нажатии.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>рендербуттонс

Эта функция выменяет стили и обновляет элементы кнопки чтения в документе. Если указан ```options.elements``` , то эта функция будет отображать кнопки в ```options.elements``` . В противном случае кнопки будут отображены в элементах документа, имеющих класс ```immersive-reader-button``` .

Эта функция автоматически вызывается пакетом SDK при загрузке окна.

Дополнительные параметры отрисовки см. в разделе Дополнительные [атрибуты](#optional-attributes) .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Параметры

| Имя | Тип | Описание |
| ---- | ---- |------------ |
| `options` | [рендербуттонсоптионс](#renderbuttonsoptions) | Параметры для настройки определенного поведения функции Рендербуттонс. Необязательный элемент. |

## <a name="types"></a>Типы

### <a name="content"></a>Content

Содержит содержимое, отображаемое в иммерсивное средство чтения.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Блок

Один блок данных, который будет передан в содержимое иммерсивное средство чтения.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>лаунчреспонсе

Содержит ответ от вызова `ImmersiveReader.launchAsync` .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Перечисление Кукиеполици

Перечисление, используемое для задания политики использования файлов cookie для иммерсивного модуля чтения. См. раздел [Параметры](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Поддерживаемые типы MIME

| Тип MIME | Описание |
| --------- | ----------- |
| text/plain | Обычный текст. |
| text/html | Содержимое в виде HTML. [Дополнительные сведения](#html-support)|
| Application/масмл + XML | Язык математической разметки (Масмл). [Дополнительные сведения](./how-to/display-math.md).
| приложение/vnd. openxmlformats-officeDocument. WordprocessingML. Document | Документ в формате Microsoft Word. docx.

### <a name="html-support"></a>Поддержка HTML

| HTML | Поддерживаемое содержимое |
| --------- | ----------- |
| Стили шрифтов | Полужирный, курсив, подчеркнутый, код, Зачеркнутый, надстрочный, нижний индекс |
| Неупорядоченные списки | Диск, круг, квадрат |
| Упорядоченные списки | Десятичная, верхняя-альфа, Нижняя-альфа, верхняя-латиница, строчная латиница |

Неподдерживаемые теги будут подготовлены к просмотру сравнимо. Изображения и таблицы в настоящее время не поддерживаются.

### <a name="options"></a>Параметры

Содержит свойства, которые настраивают определенное поведение иммерсивное средство чтения.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>рендербуттонсоптионс

Параметры для отрисовки иммерсивного кнопок чтения.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Error

Содержит сведения об ошибке.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Коды ошибок

| Код | Описание |
| ---- | ----------- |
| BadArgument | Указан недопустимый аргумент, см `message` . Дополнительные сведения. |
| Время ожидания | Не удалось загрузить иммерсивное средство чтения в течение указанного времени ожидания. |
| TokenExpired | Срок действия заданного маркера истек. |
| Ожидает повтора | Превышено ограничение скорости вызовов. |

## <a name="launching-the-immersive-reader"></a>Запуск иммерсивное средство чтения

Пакет SDK предоставляет стиль по умолчанию для кнопки запуска иммерсивное средство чтения. `immersive-reader-button`Для включения этого стиля используйте атрибут класса. Дополнительные сведения см. в [этой статье](./how-to-customize-launch-button.md).

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Необязательные атрибуты

Используйте следующие атрибуты, чтобы настроить внешний вид и поведение кнопки.

| Атрибут | Описание |
| --------- | ----------- |
| `data-button-style` | Задает стиль кнопки. Возможные значения: `icon`, `text` или `iconAndText`. По умолчанию имеет значение `icon`. |
| `data-locale` | Задает языковой стандарт. Например, `en-US` или `fr-FR`. По умолчанию используется английский язык `en` . |
| `data-icon-px-size` | Задает размер значка в пикселях. По умолчанию используется 20px. |

## <a name="browser-support"></a>Поддержка браузеров

Используйте последние версии следующих браузеров, чтобы получить лучшие впечатления от работы с иммерсивное средство чтения.

* Microsoft Edge
* Internet Explorer 11;
* Google Chrome
* Mozilla Firefox
* Apple Safari;

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [пакет SDK иммерсивного средства чтения на сайте GitHub](https://github.com/microsoft/immersive-reader-sdk).
* [Краткое руководство. Создание веб-приложения, запускающего иммерсивное средство чтения (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
