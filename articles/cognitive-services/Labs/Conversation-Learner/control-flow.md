---
title: Поток управления приложением Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Узнайте больше о потоке управления приложением Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec839c1a930ffbe73989149360f1b02866a3c50
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705308"
---
## <a name="control-flow"></a>Поток управления

В этом документе описывается поток управления приложением Conversation Learner (CL), как показано на следующей схеме.

![](media/controlflow.PNG)

1. Пользователь вводит слово или фразу в боте, например what's the weather in Seattle? (Какая погода в Сиэтле?).
1. CL передает введенные пользователем данные в модель машинного обучения, которая извлекает сущности.
   - Эта модель строится приложением Conversation Learner и размещается на сайте www.luis.ai.
1. Любые извлеченные сущности и введенный пользователем текст передаются в метод обратного вызова обнаружения сущностей в коде бота.
    - Этот код может задавать, удалять или обрабатывать значения сущностей.
1. Нейронная сеть CL затем принимает выходные данные извлечения сущностей и введенные пользователем данные, а затем оценивает все действия, определенные в боте.
   - В этом примере действие с наибольшей вероятностью — предоставить прогноз погоды:

     ![](media/controlflow_forecast.PNG)

1. Выбранное действие в этом случае требует вызова API для получения прогноза погоды. 
1. Затем вызывается этот API, который зарегистрирован с помощью метода CL.AddCallback.  Результат этого API затем возвращается пользователю в виде сообщения — например, Sunny with a high of 67 (Солнечно с температурой до 67 по Фаренгейту).
1. Затем выполняется вызов нейронной сети для определения следующего действия на основе предыдущего шага.
1. Нейронная сеть затем прогнозирует следующий набор возможных действий, а выбранное действие представляется пользователю — в этом примере в виде вопроса Anything else? (Что-нибудь еще?).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Обучение с помощью Conversation Learner](./how-to-teach-cl.md)
