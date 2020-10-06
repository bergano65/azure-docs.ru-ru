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
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742066"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Как предотвратить перевод содержимого с помощью транслятора

Переводчик позволяет пометить содержимое так, чтобы оно не переводилось. Например, таким образом можно пометить код, название торговой марки, а также слово или фразу, которые при локализации теряют смысл.

## <a name="methods-for-preventing-translation"></a>Способы запрета перевода

1. Пометьте содержимое с помощью `notranslate`. Это можно сделать, только если входные Тексттипе заданы в формате HTML.

   Пример.

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Пометьте содержимое с помощью `translate="no"`. Работает только в том случае, если входной Тексттипе задан как HTML

   Пример

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
