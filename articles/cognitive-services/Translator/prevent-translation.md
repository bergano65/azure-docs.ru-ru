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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f3bf784898f7f51beea890d8d2a8401af1403fbc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888118"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Запрет перевода содержимого с помощью API перевода текстов

API перевода текстов позволяет помечать содержимое тегами, чтобы оно не переводилось. Например, можно пометить тегами код, название торговой марки, а также слово или фразу, которая не имеет смысла при локализации.

## <a name="methods-for-preventing-translation"></a>Способы запрета перевода
1. Для экранирования используйте тег Twitter @somethingtopassthrough или #somethingtopassthrough. Отмените экранирование после перевода.

2. Пометьте содержимое с помощью `notranslate`.

   Пример:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Используйте [динамический словарь](dynamic-dictionary.md), чтобы настроить требуемый перевод.

4. Не передавайте строку в API перевода текстов для перевода.

5. Пользовательский переводчик. Используйте [словарь в пользовательском трансляторе](custom-translator/what-is-dictionary.md) , чтобы предписывает перевод фразы с вероятностью 100%.


## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Запрет перевода при вызове API перевода текстов](reference/v3-0-translate.md)
