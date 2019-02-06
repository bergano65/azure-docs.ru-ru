---
title: Фильтр ненормативной лексики — API перевода текстов
titlesuffix: Azure Cognitive Services
description: Используйте фильтр ненормативной лексики в API перевода текстов.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 5ff6666eceeaee2296c6323eaa4e7201841a1526
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212466"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Добавление фильтра ненормативной лексики в API перевода текстов

Обычно служба переводов сохраняет в переводе ненормативную лексику, которая присутствует в источнике. Степень ненормативной лексики и контекст, который делает слова оскорбительными, отличаются между культурами. В результате степень оскорбительности лексики на целевом языке может усиливаться или уменьшаться.

Если необходимо избежать ненормативной лексики при переводе, даже если она есть в источнике, можно использовать параметр фильтрации ненормативной лексики, доступный в методе Translate(). Этот параметр позволяет выбрать, хотите ли вы удалить ненормативную лексику, пометить ее соответствующими тегами или не предпринимать никаких действий.

Метод Translate() принимает параметр "options", который содержит новый элемент ProfanityAction. Принятыми значениями для элемента ProfanityAction являются "NoAction" (пропущено), "Marked" (помечено) и "Deleted" (удалено).

## <a name="accepted-values-of-profanityaction-and-examples"></a>Принятые значения ProfanityAction и примеры
|Значение ProfanityAction | Действие | Пример: Исходный язык — японский | Пример: Целевой язык — английский|
| :---|:---|:---|:---|
| NoAction | По умолчанию. Аналогично отсутствию параметра. Ненормативная лексика переходит из источника в целевой объект. | 彼は変態です。 | Он подонок. |
| Marked | Оскорбительные слова выделены XML тегами \<profanity> ... \</profanity>. | 彼は変態です。 | Он \<нецензурная лексика>jerk\</profanity >. |
| Deleted | Оскорбительные слова удаляются из выходных данных без замены. | 彼は。 | Он — . |

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Применение фильтра ненормативной лексики с помощью вызова API переводчика](reference/v3-0-translate.md)
