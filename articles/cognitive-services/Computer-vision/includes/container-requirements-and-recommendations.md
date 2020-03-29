---
title: Требования к контейнеру и рекомендации
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481775"
---
> [!NOTE]
> Требования и рекомендации основаны на тестах с одним запросом в секунду, используя 8-МБ изображение отсканированного делового письма, которое содержит 29 строк и в общей сложности 803 символов.

В следующей таблице описывается минимальное и рекомендуемое распределение ресурсов для каждого контейнера Read.

| Контейнер | Минимальные | Рекомендуемая |Tps<br>(Минимальный, максимальный)|
|-----------|---------|-------------|--|
| Чтение | 1 ядра, память 8 ГБ, 0,24 TPS | 8 ядер, 16-ГБ памяти, 1,17 TPS | 0.24, 1.17 |

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS - транзакции в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.
