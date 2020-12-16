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
ms.openlocfilehash: bf8923c1090669caa46ef51a26418933b1cda023
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563438"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Как предотвратить перевод содержимого с помощью транслятора

Переводчик позволяет пометить содержимое так, чтобы оно не переводилось. Например, таким образом можно пометить код, название торговой марки, а также слово или фразу, которые при локализации теряют смысл.

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

2. Пометьте содержимое с помощью `translate="no"`. Работает только в том случае, если входной Тексттипе задан как HTML

   Пример

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Используйте [динамический словарь](dynamic-dictionary.md), чтобы настроить требуемый перевод.

4. Не передавайте строку транслятору для перевода.

5. Пользовательский переводчик. Используйте [словарь в пользовательском трансляторе](custom-translator/what-is-dictionary.md) , чтобы предписывает перевод фразы с вероятностью 100%.


## <a name="next-steps"></a>Дальнейшие шаги
> [!div class="nextstepaction"]
> [Преобразование текста с помощью операции преобразования](reference/v3-0-translate.md)
