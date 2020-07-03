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
ms.openlocfilehash: 514d1197c5e6d0f4c0f38faf24c6c2522d489820
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400945"
---
В рамках этого краткого руководства вы будете использовать REST API для распознавания речи из файлов в процессе пакетной обработки, в котором транскрибирование речи выполняется без взаимодействия со стороны пользователя. Таким образом вы получаете простую модель программирования без необходимости в управлении параллелизмом, пользовательскими моделями речи или другими элементами. Она обеспечивает расширенные возможности управления и эффективное использование ресурсов службы речи Azure.

Дополнительные сведения о доступных параметрах и сведениях о конфигурации см. в статье о [пакетном транскрибировании](../../../batch-transcription.md).

В следующем кратком руководстве вы сможете ознакомиться с примером использования.
