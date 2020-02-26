---
title: Коды ошибок динамических событий служб мультимедиа Azure | Документация Майкрософт
description: В этой статье перечислены коды динамических событий.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599474"
---
# <a name="media-services-live-event-error-codes"></a>Коды ошибок динамических событий служб мультимедиа

В таблице ниже перечислены коды [динамических событий](live-events-outputs-concept.md) .

|Ошибка|Описание|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Эта ошибка возникает, когда входящий кодировщик отправляет потоки, превышающие 30fps для кодирования динамических событий и каналов.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Эта ошибка возникает, когда входящий кодировщик отправляет потоки, превышающие следующие разрешения: 1920x1088 для кодирования динамических событий и каналов и 4096 x 2160 для сквозных активных событий и каналов.|

## <a name="see-also"></a>См. также:

[Коды ошибок конечной точки потоковой передачи (исходного кода)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Следующие шаги

[Руководство. потоковая передача в реальном времени с помощью служб мультимедиа](stream-live-tutorial-with-api.md)
