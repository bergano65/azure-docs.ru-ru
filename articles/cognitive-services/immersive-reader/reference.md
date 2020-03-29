---
title: Погружение Читатель SDK Справка
titleSuffix: Azure Cognitive Services
description: В Иммерсивном Reader SDK содержится библиотека JavaScript, которая позволяет интегрировать Immersive Reader в ваше приложение.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156409"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Иммерсивный читатель SDK Справочник

В Иммерсивном Reader SDK содержится библиотека JavaScript, которая позволяет интегрировать Immersive Reader в ваше приложение.

## <a name="functions"></a>Функции

SDK предоставляет функции:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Запускает Immersive Reader `iframe` в вашем веб-приложении.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Параметры

| name | Тип | Описание |
| ---- | ---- |------------ |
| `token` | строка | Токен аутентификации Azure AD. |
| `subdomain` | строка | Пользовательский поддомен ресурса Immersive Reader в Azure. |
| `content` | [Содержимое](#content) | Объект, содержащий содержимое, отображаемый в Immersive Reader. |
| `options` | [Параметры](#options) | Варианты настройки определенного поведения иммерсивного читателя. Необязательный параметр. |

### <a name="returns"></a>Результаты

Возвращает `Promise<LaunchResponse>`, который решает, когда погружение Reader загружается. Разрешения `Promise` на [`LaunchResponse`](#launchresponse) объект.

### <a name="exceptions"></a>Исключения

Возврат `Promise` будет отклонен с [`Error`](#error) помощью объекта, если Immersive Reader не загрузится. Для получения дополнительной [информации смотрите коды ошибок](#error-codes).

## <a name="close"></a>закрыть

Закрывает иммерсивный читатель.

Пример использования для этой функции— если кнопка ```hideExitButton: true``` выхода скрыта путем настройки в [опциях.](#options) Затем другая кнопка (например, задняя стрелка мобильного заголовка) может вызвать эту ```close``` функцию при нажатии.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>визуализацияКнопки

Эта функция стили и обновления элементов кнопки immersive Reader документа. Если ```options.elements``` предусмотрено, то эта функция ```options.elements```будет визуализировать кнопки внутри . В противном случае кнопки будут отображаться в элементах ```immersive-reader-button```документа, которые имеют класс.

Эта функция автоматически вызывается SDK при загрузке окна.

См [Дополнительные атрибуты](#optional-attributes) для более вариантов рендеринга.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Параметры

| name | Тип | Описание |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Параметры настройки определенного поведения функции renderButtons. Необязательный параметр. |

## <a name="types"></a>Типы

### <a name="content"></a>Содержимое

Содержит содержимое, отображаемые в Immersive Reader.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Блок

Один кусок данных, которые будут переданы в содержимое immersive Reader.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Содержит ответ от вызова `ImmersiveReader.launchAsync`.

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy enum

Enum, используемый для настройки политики для использования cookie Immersive Reader. Посмотреть [варианты](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Поддерживаемые типы MIME

| Тип MIME | Описание |
| --------- | ----------- |
| text/plain | Обычный текст. |
| text/html | Содержимое в виде HTML. [Дополнительные сведения](#html-support)|
| приложение/математика | Математическая разметка языка (MathML). Ознакомьтесь с [дополнительными сведениями](./how-to/display-math.md).
| приложение/vnd.openxmlformats-officedocument.wordprocessingml.document | Документ формата Microsoft Word .docx.

### <a name="html-support"></a>Поддержка HTML

| HTML | Поддерживаемый контент |
| --------- | ----------- |
| Стили шрифтов | Смелый, курсив, Подчеркивание, Код, Strikethrough, Суперскрипт, Подкрипт |
| Неупорядоченные списки | Диск, Круг, Площадь |
| Заказанные списки | Десятичная, Верхне-Альфа, Нижняя Альфа, Верхне-Римская, Нижне-Римская |
| Гиперссылки | Скоро |

Неподдерживаемые теги будут отображаться сопоставимо. Изображения и таблицы в настоящее время не поддерживаются.

### <a name="options"></a>Параметры

Содержит свойства, настраивающие определенное поведение иммерсивного читателя.

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

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Параметры рендеринга кнопок Immersive Reader.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Error

Содержит информацию об ошибке.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Коды ошибок

| Код | Описание |
| ---- | ----------- |
| BadArgument | Представленный аргумент является `message` недействительным, см. |
| Время ожидания | Погружение Reader не удалось загрузить в течение указанного тайм-аута. |
| TokenExpired | Срок действия поставляемого токена истек. |
| Ожидает повтора | Лимит частоты вызовов превышен. |

## <a name="launching-the-immersive-reader"></a>Запуск иммерсивного читателя

SDK обеспечивает укладку по умолчанию для кнопки для запуска Immersive Reader. Используйте `immersive-reader-button` атрибут класса, чтобы включить этот стиль. Более подробная информация читайте в [этой статье.](./how-to-customize-launch-button.md)

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Дополнительные атрибуты

Используйте следующие атрибуты для настройки внешний вид кнопки.

| Атрибут | Описание |
| --------- | ----------- |
| `data-button-style` | Устанавливает стиль кнопки. Возможные значения: `icon`, `text` или `iconAndText`. По умолчанию равен `icon`. |
| `data-locale` | Устанавливает локализ. Например, `en-US` или `fr-FR`. По умолчанию `en`на английском языке . |
| `data-icon-px-size` | Устанавливает размер значка в пиксели. По умолчанию до 20px. |

## <a name="browser-support"></a>Поддержка браузеров

Используйте самые последние версии следующих браузеров для лучшего опыта с Immersive Reader.

* Microsoft Edge
* Internet Explorer 11;
* Google Chrome
* Mozilla Firefox
* Apple Safari;

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [пакет SDK иммерсивного средства чтения на сайте GitHub](https://github.com/microsoft/immersive-reader-sdk).
* [Быстрый запуск: Создайте веб-приложение, которое запускает Immersive Reader (C)](./quickstart.md)
