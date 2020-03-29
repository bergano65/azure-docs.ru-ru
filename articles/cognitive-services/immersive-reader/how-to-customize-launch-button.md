---
title: Настройка кнопки Immersive Reader
titleSuffix: Azure Cognitive Services
description: Эта статья покажет вам, как настроить кнопку, которая запускает Immersive Reader.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946213"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Как настроить кнопку Immersive Reader

В этой статье показано, как настроить кнопку, которая запускает Immersive Reader в соответствии с потребностями вашего приложения.

## <a name="add-the-immersive-reader-button"></a>Добавить кнопку Immersive Reader

Immersive Reader SDK обеспечивает укладку по умолчанию для кнопки, которая запускает Immersive Reader. Используйте `immersive-reader-button` атрибут класса, чтобы включить этот стиль.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Настройка стиля кнопок

Используйте `data-button-style` атрибут для настройки стиля кнопки. Разрешенные значения, `icon` `text`и `iconAndText`. Значение по умолчанию — `icon`.

### <a name="icon-button"></a>Кнопка значка

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Это отображает следующее:

![Кнопка значка](./media/button-icon.png)

### <a name="text-button"></a>Кнопка текста

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Это отображает следующее:

![Кнопка значка](./media/button-text.png)

### <a name="icon-and-text-button"></a>Кнопка значка и текста

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Это отображает следующее:

![Кнопка значка](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Настройка текста кнопки

Назначай язык и текст alt `data-locale` для кнопки с помощью атрибута. По умолчанию используется английский язык.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Настройка размера значка

Размер значка Immersive Reader можно настроить `data-icon-px-size` с помощью атрибута. Это устанавливает размер значка в пикселях. Размер по умолчанию составляет 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со справочной документацией по [пакету SDK для иммерсивного средства чтения](./reference.md).