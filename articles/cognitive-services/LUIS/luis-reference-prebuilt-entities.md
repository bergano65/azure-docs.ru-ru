---
title: Все заранее построенные объекты - LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье содержатся списки готовых сущностей, включенных в службу "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219723"
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
|Турецкий|[турецкий](#turkish-entity-support)|На турецком языке нет предварительно построенных организаций|

## <a name="prediction-endpoint-runtime"></a>Время выполнения конечных точек прогнозирования

Наличие предварительно построенной сущности в определенном языке определяется версией конечного запуска точки прогнозирования.

## <a name="chinese-entity-support"></a>Поддержка сущностей для китайского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```zh-CN``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    V2, V3   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Поддержка сущностей для голландского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```nl-NL``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Поддержка сущностей для английского языка (США)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```en-US``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    V2, V3   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Поддержка сущностей для французского языка (Франция)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```fr-FR``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    V2, V3   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Поддержка сущностей для французского языка (Канада)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```fr-CA``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    V2, V3   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Поддержка сущностей для немецкого языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```de-DE``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    V2, V3   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Поддержка сущностей для итальянского языка

Итальянский заранее возраст, валюта, размер, число, процентное _разрешение_ изменено с V2 и V3 предварительного просмотра.

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```it-IT``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Поддержка сущностей для японского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```ja-JP``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, -   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, -   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, -   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Поддержка сущностей для корейского языка

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```ko-KR``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    -   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    -   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Поддержка сущностей для португальского языка (Бразилия)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```pt-BR``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    V2, V3   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Поддержка сущностей для испанского языка (Испания)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```es-ES``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2, V3   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    V2, V3   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    V2, V3   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    V2, V3   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Поддержка сущностей для испанского языка (Мексика)

Поддерживаются следующие сущности:

|Предварительно созданная сущность|```es-MX``` |
------|:------:|
[Возраст](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (денежная единица)](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>Дата<br>daterange<br>time<br>timerange   |    -   |
[Размер](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    -   |
[Отправить по электронной почте](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Номер](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[ОрдиналV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    -   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    V2, V3   |

См. примечания об [устаревших предварительно созданных сущностях](luis-reference-prebuilt-deprecated.md).

KeyPhrase недоступна во всех подкатегориях португальского языка и региональных параметров (Бразилия) — ```pt-BR```.

## <a name="turkish-entity-support"></a>Поддержка сущностей для турецкого языка

**На турецком языке нет предварительно построенных организаций.**

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

GeographyV2 и PersonName не включаются в проект Recognizers-Text. Чтобы устранить проблемы с этими предварительно созданными сущностями, отправьте [запрос в службу поддержки](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сущностях [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) и [currency](luis-reference-prebuilt-currency.md).
