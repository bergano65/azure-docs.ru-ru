---
title: Настройка кнопки "иммерсивное средство чтения"
titleSuffix: Azure Cognitive Services
description: В этой статье мы покажем, как настроить кнопку, запускающую иммерсивное средство чтения.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946213"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Настройка кнопки «иммерсивное средство чтения»

В этой статье показано, как настроить кнопку, запускающую иммерсивное средство чтения в соответствии с потребностями приложения.

## <a name="add-the-immersive-reader-button"></a>Добавление кнопки "иммерсивное средство чтения"

Пакет SDK для иммерсивное средство чтения предоставляет стиль по умолчанию для кнопки, запускающей иммерсивное средство чтения. Используйте атрибут класса `immersive-reader-button`, чтобы включить этот стиль.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Настройка стиля кнопки

Используйте атрибут `data-button-style`, чтобы задать стиль кнопки. Допустимые значения: `icon`, `text`и `iconAndText`. Значение по умолчанию — `icon`.

### <a name="icon-button"></a>Кнопка "значок"

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

При этом выводится следующее:

![Кнопка "значок"](./media/button-icon.png)

### <a name="text-button"></a>Кнопка "текст"

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

При этом выводится следующее:

![Кнопка "значок"](./media/button-text.png)

### <a name="icon-and-text-button"></a>Значок и кнопка "текст"

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

При этом выводится следующее:

![Кнопка "значок"](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Настройка текста кнопки

Настройте язык и замещающий текст для кнопки с помощью атрибута `data-locale`. По умолчанию используется английский язык.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Настройка размера значка

Размер значка иммерсивного чтения можно настроить с помощью атрибута `data-icon-px-size`. Задает размер значка в пикселях. Размер по умолчанию — 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [Справочник по пакету SDK для иммерсивное средство чтения](./reference.md)