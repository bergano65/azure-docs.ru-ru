---
title: Устранение неполадок — Персонализация
titleSuffix: Azure Cognitive Services
description: Эта статья содержит ответы на часто задаваемые вопросы об устранении неполадок персонализации.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953162"
---
# <a name="personalizer-troubleshooting"></a>Устранение неполадок персонализации

Эта статья содержит ответы на часто задаваемые вопросы об устранении неполадок персонализации.

## <a name="learning-loop"></a>Цикл обучения

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>В цикле обучения не рассматривается. Разделы справки исправить это?

В цикле обучения требуется несколько тысяч вызовов наград, прежде чем Ранжированные вызовы упорядочивают приоритеты. 

Если вы не уверены в том, как работает цикл обучения, запустите [автономную оценку](concepts-offline-evaluation.md)и примените исправленную политику обучения. 

## <a name="next-steps"></a>Дополнительная информация

[Настройка частоты обновления модели](how-to-settings.md#model-update-frequency)