---
title: Устранение неполадок — Персонализация
titleSuffix: Azure Cognitive Services
description: Сведения об устранении неполадок, связанных с персонализацией, можно найти в этой статье.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663679"
---
# <a name="personalizer-troubleshooting"></a>Устранение неполадок персонализации

Эта статья содержит ответы на часто задаваемые вопросы об устранении неполадок персонализации.

## <a name="learning-loop"></a>Цикл обучения

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>В цикле обучения не рассматривается. Как это исправить?

В цикле обучения требуется несколько тысяч вызовов наград, прежде чем Ранжированные вызовы упорядочивают приоритеты. 

Если вы не уверены в том, как работает цикл обучения, запустите [автономную оценку](concepts-offline-evaluation.md)и примените исправленную политику обучения. 

## <a name="next-steps"></a>Следующие шаги

[Настройка частоты обновления модели](how-to-settings.md#model-update-frequency)