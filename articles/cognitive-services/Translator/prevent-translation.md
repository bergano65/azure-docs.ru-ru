---
title: Запрет перевода содержимого — API перевода текстов
titlesuffix: Azure Cognitive Services
description: Запрет перевода содержимого с помощью API перевода текстов.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 02c4e90879b81e29f3972d262f36d5c6b6d8e841
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448288"
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

5. Пользовательский переводчик: Используйте [словарь в Пользовательском переводчике](custom-translator/what-is-dictionary.md), чтобы настроить перевод фразы с вероятностью в 100 %.


## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Запрет перевода при вызове API перевода текстов](reference/v3-0-translate.md)
