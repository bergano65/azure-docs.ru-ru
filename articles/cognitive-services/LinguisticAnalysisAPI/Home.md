---
title: Что такое API лингвистического анализа?
titlesuffix: Azure Cognitive Services
description: Узнайте, как API лингвистического анализа обеспечивает доступ к средствам обработки естественного языка (NLP), которые определяют структуру текста.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: overview
ms.date: 10/10/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: c9a645a16a618ae075b9a0947f394e6833a14fb6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883174"
---
# <a name="what-is-the-linguistic-analysis-api-preview"></a>Что такое API лингвистического анализа?  (предварительная версия)

> [!IMPORTANT]
> Поддержка предварительной версии Лингвистического анализа прекращена 9 августа 2018 г. Мы рекомендуем использовать [модули текстовой аналитики Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) для анализа и обработки текста.

API лингвистического анализа обеспечивает доступ к средствам обработки естественного языка (NLP), которые определяют структуру текста. Текущий выпуск поддерживает три вида анализа:

- [Разделение предложений и разметка](Sentences-and-Tokens.md)
- [Добавление тегов к частям речи](POS-tagging.md)
- [Анализ структуры фразы](Constituency-Parsing.md)

## <a name="getting-started"></a>Приступая к работе

Как только вы ознакомьтесь с основными видами анализа, перечисленными выше, используйте конечную точку для [получения списка доступных анализаторов](AnalyzersMethod.md) и их [имен](Analyzer-Names.md), а затем конечную точку для [анализа новых входных данных](AnalyzeMethod.md).
