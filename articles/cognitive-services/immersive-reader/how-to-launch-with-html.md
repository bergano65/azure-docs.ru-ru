---
title: Запуск иммерсивного средства чтения с содержимым HTML
titleSuffix: Azure Cognitive Services
description: В этой статье будет показан, как запустить Immersive Reader с HTML-контентом.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946246"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Как запустить Immersive Reader с HTML-контентом

В этой статье показано, как запустить Immersive Reader с HTML-контентом.

## <a name="prepare-the-html-content"></a>Подготовка HTML-контента

Поместите содержимое, которое вы хотите отобразить в элементе immersive Reader внутри элемента контейнера. Убедитесь, что элемент контейнера имеет уникальный. `id` Immersive Reader предоставляет поддержку основных [reference](./reference.md#html-support) элементов HTML, см.

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

## <a name="get-the-html-content-in-javascript"></a>Получить HTML-контент в JavaScript

Используйте `id` элемент контейнера, чтобы получить HTML-содержимое в коде JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Запуск immersive Reader с HTML-контентом

При `ImmersiveReader.launchAsync`вызове установите `mimeType` свойство `text/html` фрагмента для включения рендеринга HTML.

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