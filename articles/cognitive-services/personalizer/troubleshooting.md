---
title: Устранение неполадок — Персонализация
titleSuffix: Azure Cognitive Services
description: Сведения об устранении неполадок, связанных с персонализацией, можно найти в этой статье.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 9f4c4129217923f7fb32996f7447ed09a034f888
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955229"
---
# <a name="personalizer-troubleshooting"></a>Устранение неполадок персонализации

Эта статья содержит ответы на часто задаваемые вопросы об устранении неполадок персонализации.

## <a name="learning-loop"></a>Цикл обучения

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>В цикле обучения не рассматривается. Как это исправить?

В цикле обучения требуется несколько тысяч вызовов наград, прежде чем Ранжированные вызовы упорядочивают приоритеты. 

Если вы не уверены в том, как работает цикл обучения, запустите [автономную оценку](concepts-offline-evaluation.md)и примените исправленную политику обучения. 

## <a name="next-steps"></a>Следующие шаги

[Настройка частоты обновления модели](how-to-settings.md#model-update-frequency)