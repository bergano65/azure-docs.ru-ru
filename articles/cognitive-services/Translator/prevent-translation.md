---
title: Запретить перевод содержимого — переводчик
titleSuffix: Azure Cognitive Services
description: Запрет перевода содержимого с помощью транслятора. Переводчик позволяет пометить содержимое так, чтобы оно не переводилось.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996182"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Как предотвратить перевод содержимого с помощью транслятора

Переводчик позволяет пометить содержимое так, чтобы оно не переводилось. Например, можно пометить тегами код, название торговой марки, а также слово или фразу, которая не имеет смысла при локализации.

## <a name="methods-for-preventing-translation"></a>Способы запрета перевода

1. Пометьте содержимое с помощью `notranslate`. Это можно сделать, только если входные Тексттипе заданы в формате HTML.

   Пример

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Используйте [динамический словарь](dynamic-dictionary.md), чтобы настроить требуемый перевод.

3. Не передавайте строку транслятору для перевода.

4. Пользовательский переводчик. Используйте [словарь в пользовательском трансляторе](custom-translator/what-is-dictionary.md) , чтобы предписывает перевод фразы с вероятностью 100%.


## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Избегайте перевода в вызове переводчика](reference/v3-0-translate.md)
