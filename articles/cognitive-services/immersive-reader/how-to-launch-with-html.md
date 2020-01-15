---
title: Запуск иммерсивное средство чтения с содержимым HTML
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как запустить иммерсивное средство чтения с содержимым HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946246"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Запуск иммерсивное средство чтения с содержимым HTML

В этой статье показано, как запустить иммерсивное средство чтения с содержимым HTML.

## <a name="prepare-the-html-content"></a>Подготовка содержимого HTML

Поместите содержимое, которое требуется визуализировать, в иммерсивное средство чтения внутри элемента контейнера. Убедитесь, что элемент контейнера имеет уникальный `id`. Иммерсивное средство чтения предоставляет поддержку основных элементов HTML. Дополнительные сведения см. в [справочнике](./reference.md#html-support) .

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Получение HTML-содержимого в JavaScript

Используйте `id` элемента контейнера для получения HTML-содержимого в коде JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Запуск иммерсивное средство чтения с помощью HTML-содержимого

При вызове `ImmersiveReader.launchAsync`задайте для свойства `mimeType` фрагмента значение `text/html`, чтобы включить отрисовку HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [Справочник по пакету SDK для иммерсивное средство чтения](./reference.md)