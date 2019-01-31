---
title: Конфигурация по умолчанию Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Узнайте о конфигурации по умолчанию Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8050008bbae4a23f09b5fa94874a6315e798b448
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211293"
---
# <a name="default-values-and-boundaries"></a>Значения и границы по умолчанию

В этом документе описана конфигурация по умолчанию Conversation Learner, а также основные границы службы.

## <a name="default-configuration"></a>Конфигурация по умолчанию

Параметр | Значение по умолчанию
--- | --- 
Время ожидания сеанса по умолчанию | 30 минут

## <a name="boundaries"></a>Границы

Параметр | Ограничение
--- | --- 
API разработки, максимальное число вызовов HTTP в месяц | 5M
API разработки, максимальное число вызовов HTTP в секунду | 25
API сеансов, максимальное число вызовов HTTP в месяц | 500K
API сеансов, максимальное число вызовов HTTP в секунду | 10
Максимальное количество настраиваемых (не программных) сущностей на модель | См. [документацию по границам LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries). На практике реальное количество может быть немного меньше.
Максимальное количество предварительно созданных сущностей на модель | См. [документацию по границам LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries).
Максимальное (общее) количество сущностей на модель | 100
Максимальное количество действий на модель | 32
Максимальное количество обучений на модель | 1000
Максимальное число реплик пользователя на диалог для обучения | 100
Максимальное количество записей диалогов на модель | Это ограничение предварительно не задано, но записанные диалоги сохраняются только на фиксированный период времени, после чего они удаляются.  Кроме того, пользовательский интерфейс Conversation Learner одновременно показывает 100 записанных диалогов. 
Максимальное количество моделей на пользователя | Нет предварительно заданного ограничения.
Максимальное число последовательных действий без ожидания | 5 (*)

(*) После 5 последовательных действий без ожидания все действия без ожидания маскируются, и Conversation Learner будет выбирать действия из доступных действий с ожиданием.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Приступая к работе с Conversation Learner](./quickstart.md)
