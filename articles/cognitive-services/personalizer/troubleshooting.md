---
title: Устранение неполадок — Personalizer
titleSuffix: Azure Cognitive Services
description: Устранение неполадок вопросы о Personalizer можно найти в этой статье.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722248"
---
# <a name="personalizer-troubleshooting"></a>Устранение неполадок personalizer

Эта статья содержит ответы на часто задаваемые вопросы об устранении неполадок, о Personalizer.

## <a name="learning-loop"></a>Цикл обучения

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Кажется, что цикл обучения не узнать. Как это исправить?

Цикл обучения требуется несколько тысяч вызовы Reward Rank вызовы эффективно определять приоритеты. 

Если вы не знаете о том, как цикл обучения в настоящее время работает, запустите [автономной оценки](concepts-offline-evaluation.md)и примените политику исправленный обучения. 

## <a name="next-steps"></a>Следующие шаги

[Настройка частоты обновления модели](how-to-settings.md#model-update-frequency)