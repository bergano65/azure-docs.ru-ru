---
title: Ограничения на контейнеры - LUIS
titleSuffix: Azure Cognitive Services
description: Языки контейнеров LUIS, которые поддерживаются.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7fe773b35c5aba31b2fea66bd2be7b2745eac3ee
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879247"
---
# <a name="language-understanding-luis-container-limitations"></a>Ограничение контейнеров Language Understanding (LUIS)

Контейнеры LUIS имеют несколько заметных ограничений. От неподдерживаемых зависимостей до подмножества поддерживаемых языков в этой статье подробно описаны эти ограничения.

## <a name="supported-dependencies-for-latest-container"></a>Поддерживаемые зависимости для `latest` контейнера

Последний контейнер LUIS, выпущенный по цене [//build/ 2019](https://news.microsoft.com/build2019/), будет поддерживать:

* [Новые предварительно построенные домены:](luis-reference-prebuilt-domains.md)эти домены, ориентированные на предприятия, включают сущности, примеры высказываний и шаблоны. Расширьте эти домены для собственного использования.

## <a name="unsupported-dependencies-for-latest-container"></a>Неподдерживаемые зависимости для `latest` контейнера

Для [экспорта для контейнера](luis-container-howto.md#export-packaged-app-from-luis)необходимо удалить неподдерживаемые зависимости из приложения LUIS. При попытке экспорта для контейнера портал LUIS сообщает об этих неподдерживаемых функциях, которые необходимо удалить.

Приложение LUIS можно использовать, если оно **не содержит** следующие зависимости:

Неподдерживаемые конфигурации приложения|Сведения|
|--|--|
|Неподдерживаемые языки и региональные параметры контейнеров| Голландский`nl-NL`()<br>Японский`ja-JP`()<br>Немецкий только поддерживается [с 1.0.2 токенизатор](luis-language-support.md#custom-tokenizer-versions).|
|Неподдерживаемые сущности для всех языков и региональных параметров|Предварительно созданная сущность [KeyPhrase](luis-reference-prebuilt-keyphrase.md) для всех языков и региональных параметров|
|Неподдерживаемые сущности`en-US`для английского языка () культуры|Предварительно созданные сущности [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Подготовка речи|Внешние зависимости не поддерживаются в контейнере.|
|Анализ тональности|Внешние зависимости не поддерживаются в контейнере.|
|Проверка орфографии Bing|Внешние зависимости не поддерживаются в контейнере.|

## <a name="languages-supported"></a>Поддерживаемые языки

Контейнеры LUIS поддерживают подмножество [языков, поддерживаемых](luis-language-support.md#languages-supported) собственно LUIS. Контейнеры LUIS способны понимать высказывания на следующих языках:

| Язык | Локаль | Предварительно созданная предметная область | Предварительно созданная сущность | Рекомендации по списку фраз | **[Текстовая аналитика](../text-analytics/language-support.md)<br>(Тональность и<br>ключевые слова)|
|--|--|:--:|:--:|:--:|:--:|
| Английский (США) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[китайский](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Французский (Франция) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Французский (Канада) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Немецкий |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Итальянский |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Корейский |`ko-KR` | ✔️ | ❌ | ❌ | *Только ключевая фраза* |
| Португальский (Бразилия) |`pt-BR` | ✔️ | ✔️ | ✔️ | не все вложенные языки и региональные параметры |
| испанский (Испания) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Испанский (Мексика)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Турецкий | `tr-TR` |✔️| ❌ | ❌ | *Только настроения* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]