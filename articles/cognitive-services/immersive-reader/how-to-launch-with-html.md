---
title: Запуск иммерсивного средства чтения с содержимым HTML
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как запустить иммерсивное средство чтения с содержимым HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.custom: devx-track-js
ms.openlocfilehash: d028e9ef2087b866b3c168483d55556e38f9a7cd
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636567"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Запуск иммерсивное средство чтения с содержимым HTML

В этой статье показано, как запустить иммерсивное средство чтения с содержимым HTML.

## <a name="prepare-the-html-content"></a>Подготовка содержимого HTML

Поместите содержимое, которое требуется визуализировать, в иммерсивное средство чтения внутри элемента контейнера. Убедитесь, что элемент контейнера имеет уникальное значение `id` . Иммерсивное средство чтения предоставляет поддержку основных элементов HTML. Дополнительные сведения см. в [справочнике](./reference.md#html-support) .

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

Используйте `id` элемент контейнера для получения HTML-содержимого в коде JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Запуск иммерсивное средство чтения с помощью HTML-содержимого

При вызове `ImmersiveReader.launchAsync` Задайте для свойства фрагмента значение `mimeType` `text/html` , чтобы разрешить HTML-код отрисовки.

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

* Ознакомьтесь со справочной документацией по [пакету SDK для иммерсивного средства чтения](./reference.md).