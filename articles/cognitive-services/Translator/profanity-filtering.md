---
title: Фильтр ненормативной лексики — API перевода текстов
titleSuffix: Azure Cognitive Services
description: Используйте фильтрацию ненормативной лексики, чтобы определить уровень ненормативной лексики, переведенный в текст в API перевода текстов Cognitive Services Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836229"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Добавление фильтра ненормативной лексики в API перевода текстов

Обычно служба переводов сохраняет в переводе ненормативную лексику, которая присутствует в источнике. Степень ненормативной лексики и контекст, который делает слова оскорбительными, отличаются между культурами. В результате степень оскорбительности лексики на целевом языке может усиливаться или уменьшаться.

Если необходимо избежать ненормативной лексики при переводе, даже если она есть в источнике, можно использовать параметр фильтрации ненормативной лексики, доступный в методе Translate(). Этот параметр позволяет выбрать, хотите ли вы удалить ненормативную лексику, пометить ее соответствующими тегами или не предпринимать никаких действий.

Метод Translate() принимает параметр "options", который содержит новый элемент ProfanityAction. Принятыми значениями для элемента ProfanityAction являются "NoAction" (пропущено), "Marked" (помечено) и "Deleted" (удалено).

## <a name="accepted-values-of-profanityaction-and-examples"></a>Принятые значения ProfanityAction и примеры
|Значение ProfanityAction | Действие | Пример: Источник — японский | Пример: Целевой объект — английский|
| :---|:---|:---|:---|
| NoAction | По умолчанию. Аналогично отсутствию параметра. Ненормативная лексика переходит из источника в целевой объект. | 彼は変態です。 | Он подонок. |
| Marked | Оскорбительные слова выделены XML тегами \<profanity> ... \</profanity>. | 彼は変態です。 | Он \<нецензурная лексика>jerk\</profanity >. |
| Deleted | Оскорбительные слова удаляются из выходных данных без замены. | 彼は。 | Он — . |

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Применение фильтра ненормативной лексики с помощью вызова API переводчика](reference/v3-0-translate.md)
