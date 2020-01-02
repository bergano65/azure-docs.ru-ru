---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь"
titleSuffix: Azure Cognitive Services
description: ПОДЛЕЖИТ УТОЧНЕНИЮ
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828866"
---
В этом кратком руководстве показано, как с помощью [REST API транскрибирования пакетов](../../../batch-transcription.md) распознавать речь в файлах, хранящихся в [большом двоичном объекте SAS](https://aka.ms/ignite2019/speech/placeholder). После выполнения нескольких предварительных требований распознавание речи с помощью REST API займет всего несколько шагов:
> [!div class="checklist"]
> * Отправьте запрос JSON в службу "Речь" для начала транскрибирования речи.
> * Проверьте состояние транскрибирования.
> * Загрузите результаты транскрибирования по завершении.