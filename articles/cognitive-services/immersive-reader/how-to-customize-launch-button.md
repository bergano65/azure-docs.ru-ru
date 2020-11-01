---
title: Кнопка настройки Иммерсивного средства чтения
titleSuffix: Azure Cognitive Services
description: В этой статье мы покажем, как настроить кнопку, запускающую иммерсивное средство чтения.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: b24a9dcb7a369708b1374f367186dc4d9a6ce475
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146881"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Настройка кнопки «иммерсивное средство чтения»

В этой статье показано, как настроить кнопку, запускающую иммерсивное средство чтения в соответствии с потребностями приложения.

## <a name="add-the-immersive-reader-button"></a>Добавление кнопки "иммерсивное средство чтения"

Пакет SDK для иммерсивное средство чтения предоставляет стиль по умолчанию для кнопки, запускающей иммерсивное средство чтения. `immersive-reader-button`Для включения этого стиля используйте атрибут класса.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Настройка стиля кнопки

Используйте `data-button-style` атрибут, чтобы задать стиль кнопки. Допустимые значения: `icon` , `text` и `iconAndText` . Значение по умолчанию — `icon`.

### <a name="icon-button"></a>Кнопка "значок"

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

При этом выводится следующее:

![Это кнопка отображаемого текста](./media/button-icon.png)

### <a name="text-button"></a>Кнопка "текст"

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

При этом выводится следующее:

![Это готовая к просмотру кнопка модуля чтения.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Значок и кнопка "текст"

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

При этом выводится следующее:

![Кнопка "значок"](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Настройка текста кнопки

Настройте язык и замещающий текст для кнопки с помощью `data-locale` атрибута. По умолчанию используется английский язык.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Настройка размера значка

Размер значка иммерсивного чтения можно настроить с помощью `data-icon-px-size` атрибута. Задает размер значка в пикселях. Размер по умолчанию — 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со справочной документацией по [пакету SDK для иммерсивного средства чтения](./reference.md).