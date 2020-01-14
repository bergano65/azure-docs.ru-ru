---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь"
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 495d2a8e38b58eb3ef1494e6a3a33ee1dc32d049
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468603"
---
В рамках этого краткого руководства вы будете использовать REST API для распознавания речи из файлов в процессе пакетной обработки, в котором транскрибирование речи выполняется без взаимодействия со стороны пользователя. Таким образом вы получаете простую модель программирования без необходимости в управлении параллелизмом, пользовательскими моделями речи или другими элементами. Она обеспечивает расширенные возможности управления и эффективное использование ресурсов службы речи Azure.

Дополнительные сведения об этих возможностях содержатся в статье [How to use batch transcription](../../../batch-transcription.md) (Использование пакетного транскрибирования). Подробные сведения об API доступны в [документе Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A) под заголовком `Custom Speech transcriptins`. 

В следующем кратком руководстве вы сможете ознакомиться с примером использования.
