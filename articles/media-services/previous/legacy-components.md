---
title: Устаревшие компоненты служб мультимедиа Azure | Документация Майкрософт
description: В этом разделе обсуждаются устаревшие компоненты служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: d4f4cfc005b2d5a63512245baee5230e9a26cb37
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309210"
---
# <a name="azure-media-services-legacy-components"></a>Устаревшие компоненты служб мультимедиа Azure

Со временем были внесены устойчивые улучшения и усовершенствования компонентов службы мультимедиа. В результате некоторые устаревшие компоненты были удалены. Инструкции по переносу приложения из устаревшего компонента можно найти в текущем компоненте в следующих статьях.

## <a name="legacy-components-and-migration-guidance"></a>Устаревшие компоненты и руководство по миграции

Мы сообщаем о прекращении использования *Windows Azure Media Encoder* (Ваме) и обработчиков мультимедиа *кодировщика мультимедиа Azure* (\ \ Media Encoder). Эти процессоры выпускают 30 ноября 2019.

* [Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Миграция из кодировщика мультимедиа Azure в Media Encoder Standard](migrate-azure-media-encoder.md)

Мы также объявляем объявление об устаревании *Azure Media indexer* и *Azure Media indexer 2*. [Azure Media Indexerный](media-services-index-content.md) обработчик мультимедиа будет снят с 1 октября по 2020. Процессоры мультимедиа [[Azure Media indexer 2 (Предварительная версия)](media-services-process-content-with-indexer2.md) будут сняты с 1 января 2019.  [Индексатор видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/) заменяет эти устаревшие обработчики мультимедиа.

* [Миграция с Azure Media indexer и Azure Media indexer 2 в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Следующие шаги

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](../latest/migrate-from-v2-to-v3.md)
