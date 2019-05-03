---
title: Поддержка языка — службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Службы "Речь" Azure поддерживает множество языков для преобразования речи в текст и текста в речь вместе с переводом речи. Эта статья содержит полный список языков поддерживаемых службой.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9b8e12220f220bd8183675d13e25bdcab02707fd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020840"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Поддержка языка и региона для службы речи

Для разных языков поддерживается разный набор функций служб "Речь". Все поддерживаемые языки перечислены в указанной ниже таблице.

## <a name="speech-to-text"></a>Преобразование речи в текст.

API распознавания речи Microsoft поддерживает следующие языки. Для каждого языка доступны разные уровни настройки.

  Код | Язык | [Акустическая адаптация](how-to-customize-acoustic-models.md) | [Языковая адаптация](how-to-customize-language-model.md) | [Адаптация произношения](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | арабский (Египет), современный стандартный | Нет  | Yes | Нет 
 ca-ES | Каталанский | Нет  | Нет  | Нет 
 da-DK | датский (Дания) | Нет  | Нет  | Нет 
 de-DE | немецкий (Германия) | Yes | Да | Нет 
 en-AU | английский (Австралия) | Нет  | Да | Yes
 en-CA | английский (Канада) | Нет  | Да | Yes
 en-GB | английский (Соединенное Королевство) | Нет  | Да | Yes
 en-IN | английский (Индия) | Yes | Да | Yes
 en-NZ | английский (Новая Зеландия) | Нет  | Да | Yes  
 en-US | английский (США) | Yes | Да | Yes
 es-ES | испанский (Испания) | Yes | Да | Нет 
 es-MX | Испанский (Мексика) | Нет  | Yes | Нет 
 fi-FI | финский (Финляндия) | Нет  | Нет  | Нет 
 fr-CA | французский (Канада) | Нет  | Yes | Нет 
 fr-FR | французский (Франция) | Yes | Да | Нет 
 hi-IN | хинди (Индия) | Нет  | Yes | Нет 
 it-IT | итальянский (Италия) | Yes | Да | Нет 
 ja-JP | японский (Япония) | Нет  | Yes | Нет 
 ko-KR | корейский (Корея) | Нет  | Yes | Нет 
 nb-NO | норвежский (букмол), (Норвегия) | Нет  | Нет  | Нет 
 nl-NL | нидерландский (Нидерланды) | Нет  | Yes | Нет 
 pl-PL | польский (Польша) | Нет  | Нет  | Нет 
 pt-BR | Португальский (Бразилия) | Yes | Да | Нет 
 pt-PT | Португальский (Португалия) | Нет  | Yes | Нет 
 ru-RU | русский (Россия) | Yes | Да | Нет 
 sv-SE | шведский (Швеция) | Нет  | Нет  | Нет 
 zh-CN | китайский (мандаринский, упрощенное письмо) | Yes | Да | Нет 
 zh-HK | Китайский (кантонский диалект китайского языка, традиционный) | Нет  | Yes | Нет 
 zh-TW | Китайский (тайваньский, мандаринский диалект) | Нет  | Yes | Нет 
 th-TH | Тайский (Таиланд) | Нет  | Нет  | Нет 


## <a name="text-to-speech"></a>Преобразование текста в речь

REST API преобразования текста в речь предлагает следующие голоса, каждый из которых поддерживает определенный язык и диалект, определяемые языковым стандартом.

> [!IMPORTANT]
> Стандартные, пользовательские и нейронные голосовые модели предлагаются по разным ценам. Дополнительные сведения см. на странице [Цены на Cognitive Services, службы речи](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Синтезирование голоса с помощью нейронных сетей

Нейронное преобразование текста в речь — это новый тип синтеза речи основанного на глубоких нейронных сетях. Во время использования нейронных голосовых моделей синтезированная речь почти ничем не отличается от записей человеческого голоса.

Нейронные голосовые модели могут сделать взаимодействие с чат-ботами и виртуальными помощниками более естественным и приятным. Они позволяют преобразовывать цифровой текст из электронных книг в аудиокниги, а также помогают совершенствовать автомобильные системы навигации. Эти модели отличаются человекоподобной естественной интонацией и четким произношение слов, что значительно уменьшает усталость от прослушивания при взаимодействии с системами искусственного интеллекта.

Полный список нейронных голосовых моделей и доступности по регионам см. [здесь](regions.md#standard-and-neural-voices).

Языковой стандарт | Язык | пол; | Сопоставление имени полный службы | Короткий голосовой имя
--------|----------|--------|---------|------------
de-DE | немецкий (Германия) | Female | «Microsoft Speech Server преобразование текста в речь речи (de-DE, KatjaNeural)» | «de-DE-KatjaNeural»
en-US | Английский (США) | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | Английский (США) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, JessaNeural)" | "en-US-JessaNeural"
it-IT | итальянский (Италия) | Female |«Microsoft Speech Server преобразование текста в речь речи (it-IT, ElsaNeural)» | "it-IT-ElsaNeural"
zh-CN | Китайский (континентальная часть) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Сопоставление имен полный службы или имя короткий голосовой можно использовать в запросах синтеза речи.

### <a name="standard-voices"></a>Стандартные голосовые модели

Свыше 75 стандартных голосовых моделей доступны в более 45 языках и языковых стандартах, что позволит вам преобразовать текст в синтезированную речь. Дополнительные сведения о доступности по регионам см. на [этой странице](regions.md#standard-and-neural-voices).

Языковой стандарт | Язык | пол; | Сопоставление имени полный службы | Короткий голосовой имя
-------|----------|---------|----------|----------
ar-EG\* | Арабский (Египет) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ar-EG, Hoda)" | «ar-EG-Hoda»
ar-SA | Арабский (Саудовская Аравия) | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ar-SA, Naayf)" | «ar-SA-Naayf»
bg-BG | Болгарский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (bg-BG, Ivan)" | «Иван bg BG»
ca-ES | каталанский (Испания) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
cs-CZ | Чешский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (cs-CZ, Jakub)" | «cs-CZ-Jakub»
da-DK | Датский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (da-DK, HelleRUS)" | "da-DK-HelleRUS"
de-AT | Немецкий (Австрия) | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-AT, Michael)" | "de-AT-Michael"
de-CH | Немецкий (Швейцария) | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-CH, Karsten)" | "de-CH-Karsten"
de-DE | немецкий (Германия) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-DE, Hedda)" | "de-DE-Hedda"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-DE, Stefan, Apollo)" | «de-DE-Стефан-Apollo»
el-GR | Греческий | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (el-GR, Stefanos)" | «el-GR-Stefanos»
en-AU | английский (Австралия) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-AU, Catherine)" | «en-AU-Catherine»
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
en-CA | английский (Канада) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-CA, Linda)" | «en-CA-Linda»
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-GB, Susan, Apollo)" | «en-GB-Сьюзан-Apollo»
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-GB, George, Apollo)" | «en-GB-Джордж-Apollo»
en-IE | Английский (Ирландия) | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IE, Sean)" | «en-IE-ресурс»
en-IN | английский (Индия) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | Английский (США) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, BenjaminRUS)" | «en США-BenjaminRUS»
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | испанский (Испания) |Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-ES, Pablo, Apollo)" | «es-ES-Пабло Apollo»
es-MX | Испанский (Мексика) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | Финский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr-CA | французский (Канада) |Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-CA, Caroline)" | «fr-CA-Каролина»
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
fr-CH | Французский (Швейцария)| Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-CH, Guillaume)" | «fr-CH-Гийом»
fr-FR | французский (Франция)| Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he-IL| Иврит (Израиль) | Male| "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (he-IL, Asaf)" | «he-IL-Asaf»
hi-IN | хинди (Индия) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hi-IN, Kalpana, Apollo)" | «hi-IN-Kalpana-Apollo»
| | |Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hi-IN, Kalpana)" | «hi-IN-Kalpana»
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hi-IN, Hemant)" | «hi-IN-Хеманта»
hr-HR | Хорватский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hr-HR, Matej)" | «hr-HR-Matej»
hu-HU | Венгерский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
id-ID | Индонезийский| Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (id-ID, Andika)" | «идентификатор ID-Andika»
it-IT | Итальянский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (id-ID, LuciaRUS)" | "it-IT-LuciaRUS"
ja-JP | Японский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Корейский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
ms-MY | Малайский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ms-MY, Rizwan)" | "ms-MY-Rizwan"
nb-NO | Норвежский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS"
nl-NL | Нидерландский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | Польский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Португальский (Бразилия) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Male |"Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
pt-PT | Португальский (Португалия) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
ro-RO | Румынский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ro-RO, Andrei)" | «ro-RO-Andrei»
ru-RU |Русский| Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ru-RU, Irina, Apollo)" | «ru-RU-Irina-Apollo»
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
sk-SK | Словацкий | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (sk-SK, Filip)" | «sk-SK-Filip»
sl-SI | Словенский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (sl-SI, Lado)" | «sl-SI-Lado»
sv-SE | Шведский | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
ta-IN | Тамильский (Индия) | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ta-IN, Valluvar)" | «ta-IN-Valluvar»
te-IN | Телугу (Индия) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (te-IN, Chitra)" | «te-IN-Chitra»
th-TH | Тайский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (th-TH, Pattara)" | «th-TH-Pattara»
tr-TR | Турецкий | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | Вьетнамский | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (vi-VN, An)" | "vi-VN-An"
zh-CN | Китайский (континентальная часть) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-CN, Yaoyao, Apollo)" | «zh-CN-Yaoyao-Apollo»
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-CN, Kangkang, Apollo)" | «zh-CN-Kangkang-Apollo»
zh-HK | Китайский (Гонконг, САР) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-HK, Danny, Apollo)" | «zh-HK-Дэнни-Apollo»
zh-TW | Китайский (Тайвань) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-TW, Yating, Apollo)" | «zh-TW-Yating-Apollo»
| | | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-TW, HanHanRUS)" | «zh-TW-HanHanRUS»
| | | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-TW, Zhiwei, Apollo)" | «zh-TW-Zhiwei-Apollo»

\* *ar-EG поддерживает современный арабский литературный язык (MSA).*

> [!NOTE]
> Сопоставление имен полный службы или имя короткий голосовой можно использовать в запросах синтеза речи.

### <a name="customization"></a>Настройка

Настройка голоса недоступна для de-DE, en-GB, en-IN, en US, es-MX, fr-FR, it-IT, pt-BR и zh-CN. Выберите справа языковой стандарт, который соответствует обучающих данных, необходимо обучить модель настраиваемых голосовых. Например если у вас есть данные записи произнесении на английском языке с британским с учетом диакритических знаков, выберите en-GB.  

> [!NOTE]
> Мы не поддерживают обучение модели bi языков в настраиваемый голос, за исключением китайский английский bi языков. Выберите английский китайский двуязычного, если необходимо обучить голос китайского, понимающего английский, а также. Обучение во всех языковых стандартах начинается с набором данных более 2000 фразы, за исключением en US и zh-CN, где можно начать с любого размера данных для обучения.

## <a name="speech-translation"></a>Перевод речи.

**API перевода речи** поддерживает перевод речи в речь и речи в текст для нескольких языков. Язык оригинала должен обязательно быть указан в таблице языков для преобразования речи в текст. Доступность языка перевода зависит от того, поддерживается ли для языка оригинала речь или только текст. Можно переводить входящую речь на более чем [60 языков](https://www.microsoft.com/translator/business/languages/). Подмножество этих языков доступно для [синтеза речи](language-support.md#text-languages).

### <a name="text-languages"></a>Языки с поддержкой текста

| Язык с поддержкой текста    | Код языка |
|:----------- |:-------------:|
| Африкаанс      | `af`          |
| Арабский       | `ar`          |
| Бенгальский      | `bn`          |
| Боснийский (латиница)      | `bs`          |
| Болгарский      | `bg`          |
| Кантонский (традиционное письмо)      | `yue`          |
| Каталанский      | `ca`          |
| Китайский (упрощенное письмо)      | `zh-Hans`          |
| Китайский (традиционное письмо)      | `zh-Hant`          |
| Хорватский      | `hr`          |
| Чешский      | `cs`          |
| Датский      | `da`          |
| Нидерландский      | `nl`          |
| Английский      | `en`          |
| Эстонский      | `et`          |
| Фиджийский      | `fj`          |
| Филиппинский      | `fil`          |
| Финский      | `fi`          |
| Французский      | `fr`          |
| Немецкий      | `de`          |
| Греческий      | `el`          |
| Гаитянский креольский      | `ht`          |
| Иврит      | `he`          |
| Хинди      | `hi`          |
| Хмонг дау      | `mww`          |
| Венгерский      | `hu`          |
| Индонезийский      | `id`          |
| Итальянский      | `it`          |
| Японский      | `ja`          |
| Суахили      | `sw`          |
| Клингонский      | `tlh`          |
| Клингонский (пикад)      | `tlh-Qaak`          |
| Корейский      | `ko`          |
| Латышский      | `lv`          |
| Литовский      | `lt`          |
| Малагасийский      | `mg`          |
| Малайский      | `ms`          |
| Мальтийский      | `mt`          |
| Норвежский      | `nb`          |
| Персидский      | `fa`          |
| Польский      | `pl`          |
| Португальский      | `pt`          |
| Керетарский диалект отоми      | `otq`          |
| Румынский      | `ro`          |
| Русский      | `ru`          |
| Самоанский      | `sm`          |
| Сербский (кириллица)      | `sr-Cyrl`          |
| Сербский (латиница)      | `sr-Latn`          |
| Словацкий     | `sk`          |
| Словенский      | `sl`          |
| Испанский      | `es`          |
| Шведский      | `sv`          |
| Таитянский      | `ty`          |
| Тамильский      | `ta`          |
| Тайский      | `th`          |
| Тонганский      | `to`          |
| Турецкий      | `tr`          |
| Украинский      | `uk`          |
| Урду      | `ur`          |
| Вьетнамский      | `vi`          |
| Валлийский      | `cy`          |
| Юкатекский майя      | `yua`          |


## <a name="next-steps"></a>Дальнейшие действия

* [Пробная версия службы речи Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
