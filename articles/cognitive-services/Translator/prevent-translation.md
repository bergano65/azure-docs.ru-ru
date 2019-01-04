---
title: Запрет перевода содержимого — API перевода текстов
titlesuffix: Azure Cognitive Services
description: Запрет перевода содержимого с помощью API перевода текстов.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: b012201bcb9e0aece3d7e1ddd4f771643088af76
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683351"
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


## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Запрет перевода при вызове API перевода текстов](reference/v3-0-translate.md)
