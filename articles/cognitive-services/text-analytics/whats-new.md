---
title: Новые возможности API анализа текста
titlesuffix: Text Analytics - Azure Cognitive Services
description: Узнайте о новых разработках с помощью службы Анализ текста
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 44ef6fb118be4d1110a693faded6c57bc8b4e2fd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499960"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Новые возможности в API "Анализ текста"

API анализа текста обновляется на постоянной основе. В этой статье содержатся сведения о новых выпусках и функциях, чтобы оставаться в курсе последних нововведений.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Распознавание именованных сущностей v3 — общедоступная Предварительная версия — Октябрь 2019

Следующая версия средства распознавания имен сущностей (NER) теперь доступна для общедоступной предварительной версии и обеспечивает Расширенное обнаружение и классификацию сущностей, найденных в тексте. Служба предоставляет:

* Распознавание следующих новых типов сущностей:
    * номер телефона.
    * IP-адрес

* Новая конечная точка для распознавания типов сущностей личных сведений (только на английском языке)
* Разделяйте конечные точки для распознавания сущностей и связывания сущностей.

Связывание сущностей поддерживает английский и испанский языки. Языковая поддержка NER зависит от типа сущности. Дополнительные сведения см. по ссылке ниже. 

> [!div class="nextstepaction"]
> [Дополнительные сведения о распознавании имен сущностей v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Анализ тональности v3 (общедоступная Предварительная версия) — Октябрь 2019

Следующая версия анализ тональности теперь доступна для общедоступной предварительной версии и обеспечивает значительные улучшения точности и детализации классификации и оценки текста API. Кроме того, он предоставляет:

* Автоматическая маркировка для различных тональности в тексте.
* Тональности анализ и вывод на уровне документа и предложения. 

Он поддерживает английский (`en`), японский (`ja`), китайский упрощенный (`zh-Hans`), китайский (`zh-Hant`традиционное письмо), французский (`fr`), итальянский (`it`), Испанский (`es`), голландский (`nl`), португальский (`pt`) и немецкий (`de`). и доступны в следующих регионах: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`и `West US 2`.

> [!div class="nextstepaction"]
> [Дополнительные сведения о анализ тональности v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>Следующие шаги

* [Что такое API "Анализ текста"?](overview.md)  
* [Примеры пользовательских сценариев](text-analytics-user-scenarios.md)
* [Пример. Как определить тональность с помощью Анализа текста](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Пример. Как определить язык с помощью Анализа текста](how-tos/text-analytics-how-to-language-detection.md)
* [Распознавание сущностей](how-tos/text-analytics-how-to-entity-linking.md)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](how-tos/text-analytics-how-to-keyword-extraction.md)
