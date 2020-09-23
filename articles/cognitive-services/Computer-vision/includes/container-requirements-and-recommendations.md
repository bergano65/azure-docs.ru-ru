---
title: Требования к контейнеру и рекомендации
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982089"
---
> [!NOTE]
> Требования и рекомендации основаны на тестах производительности с одним запросом в секунду с использованием изображения размером 8 МБ сканированного рабочего письма, которое содержит 29 строк, и всего 803 символов.

В следующей таблице описаны минимальное и рекомендуемое выделение ресурсов для каждого контейнера чтения.

| Контейнер | Минимальные | Рекомендуемая |
|-----------|---------|-------------|
| Чтение 3,0-Preview | 8 ядер, 16 ГБ памяти | 8 ядер, 24 ГБ памяти |
| Чтение 3,1-Preview | 8 ядер, 16 ГБ памяти | 8 ядер, 24 ГБ памяти |

* Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.
