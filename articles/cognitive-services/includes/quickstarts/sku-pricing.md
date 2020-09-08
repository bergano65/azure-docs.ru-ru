---
title: Номера SKU и цены на Cognitive Services
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321612"
---
Список номеров SKU и сведения о ценах см. ниже. 

#### <a name="multi-service"></a>Несколько служб

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| Несколько служб. См. дополнительные сведения на [странице с расценками](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Компьютерное зрение

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| Компьютерное зрение            | `ComputerVision`          |
| Ресурс прогнозирования службы "Пользовательское визуальное распознавание" | `CustomVision.Prediction` |
| Ресурс обучения службы "Пользовательское визуальное распознавание"   | `CustomVision.Training`   |
| Распознавание лиц                       | `Face`                    |
| Распознаватель документов            | `FormRecognizer`          |
| Распознаватель рукописного текста             | `InkRecognizer`           |

#### <a name="search"></a>Поиск

| Служба            | Вид                  |
|--------------------|-----------------------|
| Автозаполнение Bing   | `Bing.Autosuggest.v7` |
| Пользовательский поиск Bing | `Bing.CustomSearch`   |
| API Поиска сущностей Bing | `Bing.EntitySearch`   |
| Поиск Bing        | `Bing.Search.v7`      |
| API Проверки орфографии Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Речь

| Служба            | Вид                 |
|--------------------|----------------------|
| Службы "Речь"    | `SpeechServices`     |
| Распознавание речи | `SpeakerRecognition` |

#### <a name="language"></a>Язык

| Служба            | Вид                |
|--------------------|---------------------|
| Анализ форм | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Анализ текста     | `TextAnalytics`     |
| Преобразование текста   | `TextTranslation`   |

#### <a name="decision"></a>Решение

| Служба           | Вид               |
|-------------------|--------------------|
| Детектор аномалий  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Персонализатор      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Ценовые категории и выставление счетов

Ценовые категории (и сумма в выставленных счетах) основаны на количестве отправленных вами транзакций с использованием данных аутентификации. Каждая ценовая категория определяет:
* максимальное количество разрешенных транзакций, обрабатываемых в секунду (TPS);
* функции службы, включенные в ценовой категории;
* стоимость предопределенного количества транзакций. При превышении этого количества будет взиматься дополнительная плата, как указано в [сведениях о ценах](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) для вашей службы.

> [!NOTE]
> Многие из служб Cognitive Services имеют бесплатный уровень, который можно использовать для пробного использования службы. Чтобы использовать бесплатный уровень, выберите `F0` в качестве номера SKU для вашего ресурса.