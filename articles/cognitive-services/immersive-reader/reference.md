---
title: Справочник по пакету SDK иммерсивных чтения
titlesuffix: Azure Cognitive Services
description: Ссылка для Иммерсивных модуля чтения пакета SDK
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: bf337f8d4eac859dc39a36ba12dd9645aa23fe41
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297036"
---
# <a name="immersive-reader-sdk-reference"></a>Справочник по пакету SDK иммерсивных чтения

Иммерсивных пакета SDK для чтения — это библиотека JavaScript, которая позволяет интегрировать Иммерсивных чтения в веб-приложения.

## <a name="functions"></a>Функции Azure

Пакет SDK предоставляет одну функцию, `ImmersiveReader.launchAsync(token, data, options)`.

### <a name="launchasync"></a>launchAsync

Запускает Иммерсивных чтения в `iframe` веб-приложения.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Параметры

| Name | Тип | Описание |
| ---- | ---- |------------ |
| `token` | string | Маркер доступа, полученный из вызова `issueToken` конечной точки. |
| `resourceName` | string | Зарезервировано. Нужно задать значение `null`. |
| `content` | [Содержимое](#content) | Объект, содержащий содержимое для отображения в Иммерсивных средство чтения. |
| `options` | [Варианты](#options) | Параметры настройки определенными параметрами этих Иммерсивных средство чтения. Необязательный элемент. |

#### <a name="returns"></a>Результаты

Возвращает `Promise<HTMLDivElement>` который разрешается при загрузке Иммерсивных средство чтения. `Promise` Разрешается `div` , единственным дочерним элементом является элемент `iframe` элемент, содержащий Иммерсивных чтения страницы.

#### <a name="exceptions"></a>Исключения

Возвращенный `Promise` отклоняются с [ `Error` ](#error) объекта, если Иммерсивных средство чтения не загружается. Дополнительные сведения см. в разделе [коды ошибок](#error-codes).

## <a name="types"></a>Типы

### <a name="content"></a>Content

С содержимым для отображения в Иммерсивных средство чтения.

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
| приложения/mathml + xml | Математические Markup Language (MathML). [Узнайте больше](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Параметры

Содержит свойства, которые настраивают определенными параметрами этих Иммерсивных средство чтения.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Ошибка

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
| BadArgument | Предоставленный аргумент является недопустимым, см. в разделе `message` подробные сведения. |
| Время ожидания | Не удалось загрузить в течение заданного времени ожидания Иммерсивных средство чтения. |
| TokenExpired| Истек срок действия предоставленного токена. |

## <a name="launching-the-immersive-reader"></a>Запуск Иммерсивных чтения

Пакет SDK предоставляет стилем по умолчанию для кнопки для запуска Иммерсивных средство чтения. Используйте `immersive-reader-button` класс атрибута, чтобы включить эти действия со стилями.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Необязательные атрибуты

Используйте следующие атрибуты, чтобы настроить внешний вид кнопки.

| Атрибут | Описание |
| --------- | ----------- |
| `data-button-style` | Задает стиль кнопки. Возможные значения: `icon`, `text` или `iconAndText`. По умолчанию — `icon`. |
| `data-locale` | Задает языковой стандарт, например `en-US`, `fr-FR`. По умолчанию используется английский. |
| `data-icon-px-size` | Задает размер значка в пикселях. По умолчанию 20px. |

## <a name="browser-support"></a>Поддержка браузеров

Используйте последние версии следующих браузеров для получения наилучших результатов с помощью мощных средства чтения.

* Microsoft Edge
* Internet Explorer 11;
* Google Chrome
* Mozilla Firefox
* Apple Safari;

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [Иммерсивных чтения SDK на сайте GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Краткое руководство Создание веб-приложения, который запускает Иммерсивных чтения (C#)](./quickstart.md)