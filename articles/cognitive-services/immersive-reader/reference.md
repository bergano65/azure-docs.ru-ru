---
title: Справочник по пакету SDK для иммерсивного чтения
titleSuffix: Azure Cognitive Services
description: Справочник по пакету SDK для иммерсивное средство чтения
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 1a616bce8c161825853b1966769d9505595d95de
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688313"
---
# <a name="immersive-reader-sdk-reference"></a>Справочник по пакету SDK для иммерсивного чтения

Пакет SDK для иммерсивное средство чтения — это библиотека JavaScript, которая позволяет интегрировать иммерсивное средство чтения в веб-приложение.

## <a name="functions"></a>Функции

Пакет SDK предоставляет одну функцию `ImmersiveReader.launchAsync(token, subdomain, content, options)`.

### <a name="launchasync"></a>лаунчасинк

Запускает иммерсивное средство чтения `iframe` в веб-приложении.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Параметры

| Название | Тип | Описание |
| ---- | ---- |------------ |
| `token` | строка | Маркер проверки подлинности Azure AD. См. [руководство по проверке подлинности Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | строка | Пользовательский поддомен для иммерсивного ресурса чтения в Azure. См. [руководство по проверке подлинности Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Содержимое](#content) | Объект, содержащий содержимое, которое должно отображаться в иммерсивное средство чтения. |
| `options` | [Варианты](#options) | Параметры для настройки определенного поведения иммерсивное средство чтения. Необязательный элемент. |

#### <a name="returns"></a>Возвращает

Возвращает объект `Promise<HTMLDivElement>` , который разрешается при загрузке иммерсивное средство чтения. Объект `Promise` разрешается `div` в элемент, единственным дочерним `iframe` элементом которого является элемент, содержащий страницу иммерсивного чтения.

#### <a name="exceptions"></a>Исключения

Возвращенный `Promise` [`Error`](#error) объект будет отклонен с объектом, если не удается загрузить иммерсивное средство чтения. Дополнительные сведения см. в [кодах ошибок](#error-codes).

## <a name="types"></a>Типы

### <a name="content"></a>Content

Содержит содержимое, отображаемое в иммерсивное средство чтения.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Поддерживаемые типы MIME

| Тип MIME | Описание |
| --------- | ----------- |
| text/plain | Обычный текст. |
| Application/масмл + XML | Язык математической разметки (Масмл). [Узнайте больше](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Параметры

Содержит свойства, которые настраивают определенное поведение иммерсивное средство чтения.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
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
| бадаргумент | Указан недопустимый аргумент, `message` см. Дополнительные сведения. |
| Время ожидания | Не удалось загрузить иммерсивное средство чтения в течение указанного времени ожидания. |
| TokenExpired| Срок действия заданного маркера истек. |

## <a name="launching-the-immersive-reader"></a>Запуск иммерсивное средство чтения

Пакет SDK предоставляет стиль по умолчанию для кнопки запуска иммерсивное средство чтения. Для включения этого стиля используйте атрибут класса.`immersive-reader-button`

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Необязательные атрибуты

Используйте следующие атрибуты, чтобы настроить внешний вид и поведение кнопки.

| Атрибут | Описание |
| --------- | ----------- |
| `data-button-style` | Задает стиль кнопки. Возможные значения: `icon`, `text` или `iconAndText`. По умолчанию — `icon`. |
| `data-locale` | Задает языковой стандарт, например `en-US`, `fr-FR`. По умолчанию используется английский язык. |
| `data-icon-px-size` | Задает размер значка в пикселях. По умолчанию используется 20px. |

## <a name="browser-support"></a>Поддержка браузеров

Используйте последние версии следующих браузеров, чтобы получить лучшие впечатления от работы с иммерсивное средство чтения.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari;

## <a name="next-steps"></a>Следующие шаги

* Изучите [пакет SDK иммерсивного средства чтения на сайте GitHub](https://github.com/microsoft/immersive-reader-sdk).
* [Краткое руководство Создание веб-приложения, запускающего иммерсивное средство чтения (C#)](./quickstart.md)