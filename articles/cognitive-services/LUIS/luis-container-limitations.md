---
title: Ограничения контейнера — LUIS
titleSuffix: Azure Cognitive Services
description: Поддерживаемые языки контейнеров LUIS.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f4751440d719a542d39f8d8e83260c8cb628de0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659287"
---
# <a name="language-understanding-luis-container-limitations"></a>Ограничения контейнеров Language Understanding (LUIS)

Контейнеры LUIS имеют несколько заметных ограничений. В этой статье описываются эти ограничения от неподдерживаемых зависимостей до подмножества поддерживаемых языков.

## <a name="supported-dependencies-for-latest-container"></a>Поддерживаемые зависимости для `latest` контейнера

Последний контейнер LUIS поддерживает:

* [Новые предварительно созданные домены](luis-reference-prebuilt-domains.md). Эти корпоративные домены включают сущности, примеры фразы продолжительностью и закономерности. Расширьте эти домены для собственного использования.

## <a name="unsupported-dependencies-for-latest-container"></a>Неподдерживаемые зависимости для `latest` контейнера

Чтобы выполнить [Экспорт для контейнера](luis-container-howto.md#export-packaged-app-from-luis), необходимо удалить неподдерживаемые зависимости из приложения Luis. При попытке экспорта для контейнера портал LUIS сообщает о неподдерживаемых функциях, которые необходимо удалить.

Приложение LUIS можно использовать, если оно **не содержит** следующие зависимости:

Неподдерживаемые конфигурации приложения|Сведения|
|--|--|
|Неподдерживаемые языки и региональные параметры контейнеров| Языки нидерландского языка ( `nl-NL` ), японского ( `ja-JP` ) и немецкого ( `de-DE` ) поддерживаются только с помощью подсчета [1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Неподдерживаемые сущности для всех языков и региональных параметров|Предварительно созданная сущность [KeyPhrase](luis-reference-prebuilt-keyphrase.md) для всех языков и региональных параметров|
|Неподдерживаемые сущности для `en-US` языка и региональных параметров "Английский ()"|Предварительно созданные сущности [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Подготовка речи|Внешние зависимости не поддерживаются в контейнере.|
|Анализ тональности|Внешние зависимости не поддерживаются в контейнере.|
|Проверка орфографии Bing|Внешние зависимости не поддерживаются в контейнере.|

## <a name="languages-supported"></a>Поддерживаемые языки

Контейнеры LUIS поддерживают подмножество [языков, ПОДДЕРЖИВАЕМЫХ](luis-language-support.md#languages-supported) Luis. Контейнеры LUIS могут понимать фразы продолжительностью на следующих языках:

| Язык | Языковой стандарт | Предварительно созданная предметная область | Предварительно созданная сущность | Рекомендации по списку фраз | **[Текстовая аналитика](../text-analytics/language-support.md)<br>(Тональность и<br>ключевые слова)|
|--|--|:--:|:--:|:--:|:--:|
| Английский (США) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[китайский](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Французский (Франция) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Французский (Канада) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Немецкий |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Итальянский |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Корейский |`ko-KR` | ✔️ | ❌ | ❌ | Только *ключевая фраза* |
| Португальский (Бразилия) |`pt-BR` | ✔️ | ✔️ | ✔️ | не все вложенные языки и региональные параметры |
| Испанский (Испания) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Испанский (Мексика)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Турецкий | `tr-TR` |✔️| ❌ | ❌ | Только *тональности* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]