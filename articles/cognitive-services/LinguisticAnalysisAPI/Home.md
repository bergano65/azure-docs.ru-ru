---
title: Что такое API лингвистического анализа?
titlesuffix: Azure Cognitive Services
description: Узнайте, как API лингвистического анализа обеспечивает доступ к средствам обработки естественного языка (NLP), которые определяют структуру текста.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: overview
ms.date: 10/10/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 3051f8d25c3591d40d012f2c9041dd0e65c03d12
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226976"
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
