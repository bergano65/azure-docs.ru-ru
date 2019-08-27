---
title: Требования к контейнеру и рекомендации.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034473"
---
В следующей таблице описаны минимальное и рекомендуемое число ядер ЦП и памяти для каждого контейнера Распознавания текста.

| Контейнер | Минимум | Рекомендуется |ПЛАТЫ<br>(Минимум, максимум)|
|-----------|---------|-------------|--|
|Распознавание текста|1 ядро, 8 ГБ памяти, 0,5 TPS|2 ядра, 8 ГБ памяти, 1 Техническая спецификация|0,5, 1|

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS — транзакций в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.