---
title: Запрет перевода содержимого — API перевода текстов
titleSuffix: Azure Cognitive Services
description: Запрет перевода содержимого с помощью API перевода текстов. API перевода текстов позволяет помечать содержимое тегами, чтобы оно не переводилось.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052491"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Запрет перевода содержимого с помощью API перевода текстов

API перевода текстов позволяет помечать содержимое тегами, чтобы оно не переводилось. Например, можно пометить тегами код, название торговой марки, а также слово или фразу, которая не имеет смысла при локализации.

## <a name="methods-for-preventing-translation"></a>Способы запрета перевода

1. Пометьте содержимое с помощью `notranslate`. Это дизайн, что это работает только тогда, когда ввод textType установлен как HTML

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

3. Не передавайте строку в API перевода текстов для перевода.

4. Пользовательский переводчик: Используйте [словарь в Custom Translator,](custom-translator/what-is-dictionary.md) чтобы предписать перевод фразы со 100% вероятностью.


## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Запрет перевода при вызове API перевода текстов](reference/v3-0-translate.md)
