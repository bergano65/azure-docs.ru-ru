---
title: Ограничения контейнера — LUIS
titleSuffix: Azure Cognitive Services
description: Поддерживаемые языки контейнеров LUIS.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507861"
---
# <a name="language-understanding-luis-container-limitations"></a>Ограничения контейнеров Language Understanding (LUIS)

Контейнеры LUIS имеют несколько заметных ограничений. В этой статье описываются эти ограничения от неподдерживаемых зависимостей до подмножества поддерживаемых языков.

## <a name="supported-dependencies-for-latest-container"></a>Поддерживаемые зависимости для контейнера `latest`

Последний контейнер LUIS, выпущенный в [//буилд/2019](https://news.microsoft.com/build2019/), будет поддерживать следующее:

* [Новые предварительно созданные домены](luis-reference-prebuilt-domains.md). Эти корпоративные домены включают сущности, примеры фразы продолжительностью и закономерности. Расширьте эти домены для собственного использования.

## <a name="unsupported-dependencies-for-latest-container"></a>Неподдерживаемые зависимости для контейнера `latest`

Чтобы выполнить [Экспорт для контейнера](luis-container-howto.md#export-packaged-app-from-luis), необходимо удалить неподдерживаемые зависимости из приложения Luis. При попытке экспорта для контейнера портал LUIS сообщает о неподдерживаемых функциях, которые необходимо удалить.

Приложение LUIS можно использовать, если оно **не содержит** следующие зависимости:

Неподдерживаемые конфигурации приложения|Сведения|
|--|--|
|Неподдерживаемые языки и региональные параметры контейнеров| Голландский (`nl-NL`)<br>Японский (`ja-JP`)<br>Немецкий поддерживается только с помощью подсчета [1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Неподдерживаемые сущности для всех языков и региональных параметров|Предварительно созданная сущность [KeyPhrase](luis-reference-prebuilt-keyphrase.md) для всех языков и региональных параметров|
|Неподдерживаемые сущности для языка и региональных параметров "Английский (`en-US`)"|Предварительно созданные сущности [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Подготовка речи|Внешние зависимости не поддерживаются в контейнере.|
|Анализ мнений|Внешние зависимости не поддерживаются в контейнере.|
|Проверка орфографии Bing|Внешние зависимости не поддерживаются в контейнере.|

## <a name="languages-supported"></a>Поддерживаемые языки

Контейнеры LUIS поддерживают подмножество [языков, ПОДДЕРЖИВАЕМЫХ](luis-language-support.md#languages-supported) Luis. Контейнеры LUIS могут понимать фразы продолжительностью на следующих языках:

| язык | Языковой стандарт | Предварительно созданная предметная область | Предварительно созданная сущность | Рекомендации по списку фраз | **[Анализ текста](../text-analytics/language-support.md)<br>(Тональность и<br>ключевые слова)|
|--|--|:--:|:--:|:--:|:--:|
| Английский (США) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Китайский](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Французский (Франция) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| французский (Канада) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Немецкий |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Хинди | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Итальянский |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Корейский |`ko-KR` | ✔️ | ❌ | ❌ | Только *ключевая фраза* |
| Португальский (Бразилия) |`pt-BR` | ✔️ | ✔️ | ✔️ | не все вложенные языки и региональные параметры |
| Испанский (Испания) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Испанский (Мексика)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Турецкий | `tr-TR` |✔️| ❌ | ❌ | Только *тональности* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]