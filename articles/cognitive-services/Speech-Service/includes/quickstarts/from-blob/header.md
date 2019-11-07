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
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506102"
---
В этом кратком руководстве показано, как с помощью [REST API транскрибирования пакетов](../../../batch-transcription.md) распознавать речь в файлах, хранящихся в [большом двоичном объекте SAS](https://aka.ms/ignite2019/speech/placeholder). После выполнения нескольких предварительных требований распознавание речи с помощью REST API займет всего несколько шагов:
> [!div class="checklist"]
> * Отправьте запрос JSON в службу "Речь" для начала транскрибирования речи.
> * Проверьте состояние транскрибирования.
> * Загрузите результаты транскрибирования по завершении.