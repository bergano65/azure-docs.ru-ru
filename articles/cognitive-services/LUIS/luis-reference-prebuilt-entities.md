---
title: Полный перечень предварительно созданных сущностей
titleSuffix: Azure Cognitive Services
description: В этой статье содержатся списки готовых сущностей, включенных в службу "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: 96157511c0255774a64c0f64a333fe88a37f80d6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261434"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Сущности для языков и региональных параметров в модели LUIS

Служба "Распознавание речи" (LUIS) включает готовые сущности. При включении предварительно созданной сущности в приложение LUIS включает соответствующий прогноз сущности в ответ конечной точки. Все примеры фраз также помечаются с помощью сущности. Поведение предварительно созданных сущностей **нельзя** изменить. Если не указано иное, предварительно созданные сущности доступны во всех языковых стандартах (языках и региональных параметрах) приложений LUIS. В следующей таблице показаны предварительно созданные сущности, которые поддерживаются для каждого языка и региональных параметров.

|Язык и региональные параметры|Подкатегории языков и региональных параметров|Примечания|
|--|--|--|
|Китайский|[zh-CN](#chinese-entity-support)||
|Нидерландский|[nl-NL](#dutch-entity-support)||
|Английский|[en-US (США)](#english-american-entity-support)||
|Французский|[fr-CA (Канада)](#french-canadian-entity-support), [fr-FR (Франция)](#french-france-entity-support), ||
|Немецкий|[de-DE](#german-entity-support)||
|Итальянский|[it-IT](#italian-entity-support)||
|Японский|[ja-JP](#japanese-entity-support)||
|Корейский|[ko-KR](#korean-entity-support)||
|Португальский|[pt-BR (Бразилия)](#portuguese-brazil-entity-support)||
|Испанский|[es-ES (Испания)](#spanish-spain-entity-support), [es-MX (Мексика)](#spanish-mexico-entity-support)||
|Турецкий|[turkish](#turkish-entity-support)|Предварительно созданные сущности не поддерживается в турецком языке|

## <a name="chinese-entity-support"></a>Поддержка сущностей для китайского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```zh-CN``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Поддержка сущностей для голландского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```nl-NL``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Поддержка сущностей для английского языка (США)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```en-US``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Поддержка сущностей для французского языка (Франция)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```fr-FR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Поддержка сущностей для французского языка (Канада)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```fr-CA``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Поддержка сущностей для немецкого языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```de-DE``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Поддержка сущностей для итальянского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```it-IT``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Поддержка сущностей для японского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```ja-JP``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Поддержка сущностей для корейского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```ko-KR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    -   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    -   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Поддержка сущностей для португальского языка (Бразилия)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```pt-BR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Поддержка сущностей для испанского языка (Испания)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```es-ES``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Поддержка сущностей для испанского языка (Мексика)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```es-MX``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    -   | 
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Число](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    -   | 
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |

См. примечания об [устаревших предварительно созданных сущностях](luis-reference-prebuilt-deprecated.md).

KeyPhrase недоступна во всех подкатегориях португальского языка и региональных параметров (Бразилия) — ```pt-BR```.

## <a name="turkish-entity-support"></a>Поддержка сущностей для турецкого языка

**Нет готовых сущностей поддерживается в турецком языке.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Участие в создании предварительно созданных сущностей для языков и региональных параметров
Предварительно созданные сущности разрабатываются в проекте с открытым кодом Recognizers-Text. Приглашаем к [участию](https://github.com/Microsoft/Recognizers-Text) в проекте. Этот проект содержит примеры единиц валюты для различных языков и региональных параметров. 

GeographyV2 и PersonName не включаются в проект Recognizers-Text. Чтобы устранить проблемы с этими предварительно созданными сущностями, отправьте [запрос в службу поддержки](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сущностях [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) и [currency](luis-reference-prebuilt-currency.md). 
