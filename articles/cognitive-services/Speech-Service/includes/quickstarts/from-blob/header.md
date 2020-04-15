---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь"
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/13/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 567d8cec10543f233a384635daba1474b41bde9b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80573190"
---
В рамках этого краткого руководства вы будете использовать REST API для распознавания речи из файлов в процессе пакетной обработки, в котором транскрибирование речи выполняется без взаимодействия со стороны пользователя. Таким образом вы получаете простую модель программирования без необходимости в управлении параллелизмом, пользовательскими моделями речи или другими элементами. Она обеспечивает расширенные возможности управления и эффективное использование ресурсов службы речи Azure.

Дополнительные сведения о доступных параметрах и сведениях о конфигурации см. в статье о [пакетном транскрибировании](../../../batch-transcription.md).

В следующем кратком руководстве вы сможете ознакомиться с примером использования.
