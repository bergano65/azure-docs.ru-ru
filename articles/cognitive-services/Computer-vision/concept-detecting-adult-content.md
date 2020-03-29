---
title: Взрослый, колоритный, гори содержание - Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Концепции, связанные с обнаружением контента для взрослых в изображениях с помощью APi компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718510"
---
# <a name="detect-adult-content"></a>Обнаружение содержимого для взрослых

Computer Vision может обнаруживать материал для взрослых в изображениях, так что разработчики могут ограничить отображение этих изображений в своем программном обеспечении. Флаги содержимого применяются с рейтингом от единицы до нуля, чтобы разработчики могли интерпретировать результаты в зависимости от их собственных предпочтений.

> [!NOTE]
> Большая часть этой функциональности предлагается службой [модератора содержимого Azure.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) Ознакомьтесь также с другими решениями для более тщательной модерации контента, например с модерацией текста или рабочими процессами пользовательской проверки.

## <a name="content-flag-definitions"></a>Определения флага содержимого

В "взрослой" классификации есть несколько различных категорий:

- **Взрослые** изображения определяются как те, которые явно сексуального характера и часто изображают наготу и половые акты.
- Под изображениями **непристойного характера** понимаются изображения, имеющие неприличный характер, но зачастую менее откровенные, чем изображения категории **Для взрослых**.
- **Гори** изображения определяются как те, которые изображают Гор.

## <a name="use-the-api"></a>Использование API

Вы можете обнаружить содержимое для взрослых с помощью API [анализа изображений.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) При `Adult` добавлении значения параметра **запроса visualFeatures** API возвращает&mdash;`isAdultContent`три `isRacyContent`свойства `isGoryContent` &mdash;boolean и в своем ответе JSON. Метод также возвращает&mdash;`adultScore`соответствующие `goreScore` &mdash;свойства, `racyScore`и которые представляют оценки доверия между нулем и одним для каждой соответствующей категории.

- [Быстрый запуск: Проанализируйте изображение (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Быстрый запуск: Проанализируйте изображение (REST API)](./quickstarts/csharp-analyze.md)
