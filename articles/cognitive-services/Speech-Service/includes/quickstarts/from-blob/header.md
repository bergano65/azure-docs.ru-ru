---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь"
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/13/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9ced792ab076c312c827d4a74c08cfa7971b7e17
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569427"
---
В рамках этого руководства вы будете использовать REST API пакетного транскрибирования для распознавания речи из файлов в процессе пакетной обработки. в котором транскрибирование речи выполняется без взаимодействия со стороны пользователя. Таким образом вы получаете простую модель программирования без необходимости в управлении параллелизмом, пользовательскими моделями речи или другими элементами. Она обеспечивает расширенные возможности управления и эффективное использование ресурсов службы речи Azure.

Дополнительные сведения о доступных параметрах и сведениях о конфигурации см. в статье о [пакетном транскрибировании](../../../batch-transcription.md).

В следующем кратком руководстве вы сможете ознакомиться с примером использования.
