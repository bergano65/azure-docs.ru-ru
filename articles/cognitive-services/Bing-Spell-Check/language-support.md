---
title: Поддержка языков — API проверки орфографии Bing
titleSuffix: Azure Cognitive Services
description: Список естественных языков, поддерживаемых API проверки орфографии Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 38bf38f47e4430c847e54bd89f6bf3cd7be50527
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367086"
---
# <a name="language-and-region-support-for-bing-spell-check-api"></a>Поддержка языков и регионов в API проверки орфографии Bing

> [!WARNING]
> API-интерфейсы поиска Bing перемещаются из Cognitive Services в Поиск Bing службы. Начиная с **30 октября 2020** , все новые экземпляры Поиск Bing должны быть подготовлены, следуя описанному [здесь](https://aka.ms/cogsvcs/bingmove)процессу.
> API-интерфейсы поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до конца Соглашение Enterprise, в зависимости от того, что происходит раньше.
> Инструкции по миграции см. в разделе [Поиск Bing Services](https://aka.ms/cogsvcs/bingmigration).

В API проверки орфографии Bing поддерживаются перечисленные ниже языки (только в режиме `spell`).

Учтите, что для работы с любым языком, кроме `en-US`, необходимо задать `mkt`, а также `Accept-Language` или `setLang`, как описано в [справочнике по API проверки орфографии версии 7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference).

| Язык    | Код языка |
|:----------- |:-------------:|
| Арабский      | `ar`          |
| Китайский (Китай)     | `zh-CN`          |
| китайский (Гонконг, CАР)    | `zh-HK`          |
| Китайский (Тайвань)     | `zh-TW`          |
| Датский      | `da`          |
| Нидерландский (Бельгия)       | `nl-BE`          |
| Нидерландский (Нидерланды)      | `nl-NL`          |
| Английский (Австралия)    | `en-AU`          |
| Английский (Канада)     | `en-CA`          |
| Английский (Индия)    | `en-IN`          |
| Английский (Индонезия)     | `en-ID`          |
| Английский (Малайзия)     | `en-MY`          |
| Английский (Новая Зеландия)    | `en-NZ`          |
| Английский (Филиппины)     | `en-PH`          |
| Английский (Южная Африка)    | `en-ZA`          |
| Английский (Великобритания)    | `en-GB`          |
| Английский (США)    | `en-US`          |
| Финский     | `fi`          |
| Французский (Бельгия)     | `fr-BE`          |
| Французский (Канада)     | `fr-CA`          |
| Французский (Франция)     | `fr-FR`          |
| Французский (Швейцария)      | `fr-CH`          |
| Немецкий (Австрия)      | `de-AT`          |
| Немецкий (Германия)      | `de-DE`          |
| Немецкий (Швейцария)      | `de-CH`          |
| Итальянский     | `it`          |
| Японский    | `ja`          |
| Корейский      | `ko`          |
| Норвежский   | `no`          |
| Польский      | `pl`          |
| Португальский (Бразилия)   | `pt-BR`|
| Португальский (Португалия) | `pt-PT`|
| русском языке     | `ru`          |
| Испанский (Аргентина)    | `es-AR`          |
| Испанский (Чили)     | `es-CL`          |
| Испанский (Мексика)    | `es-MX`          |
| испанский (Испания)    | `es-ES`          |
| Испанский (США)    | `es-US`          |
| Шведский     | `sv`          |
| Турецкий     | `tr`          |

## <a name="see-also"></a>См. также

- [Страница документации по службам Cognitive Services](../index.yml)
- [Страница продукта Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)