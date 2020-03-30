---
title: Сравнение видео индекса и Azure Media Services v3 пресетов
description: В этой статье сравниваются возможности видеоиндекса и пресетов Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602190"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Сравнение предустановок Служб мультимедиа версии 3 и Индексатора видео 

В этой статье сравниваются возможности **API Индексатора видео** и **API Служб мультимедиа (версия 3)**. 

В настоящее время существует совпадение между функциями, предлагаемыми [AIS Video Indexer](https://api-portal.videoindexer.ai/) и [AIS Media Services v3.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) В следующей таблице представлены сведения о текущих различиях и сходстве. 

## <a name="compare"></a>Сравнение

|Компонент|API Индексатора видео |Предустановки анализатора видео и звука<br/>в Службах мультимедиа Azure, версия 3|
|---|---|---|
|Аналитические сведения о мультимедиа|[Расширенная архитектура](video-indexer-output-json-v2.md) |[Основы](../latest/intelligence-concept.md)|
|Возможности|Ознакомьтесь с полным списком поддерживаемых возможностей в следующей статье: <br/> [Обзор](video-indexer-overview.md)|Возвращает только аналитические сведения о видео|
|Выставление счетов|[Ценообразование медиа-услуг](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Ценообразование медиа-услуг](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Соответствие нормативным требованиям|Для получения самых последних обновлений соответствия посетите [сайт Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) и поищите «Видео индексатор», чтобы узнать, соответствует ли он интересуемому сертификату.|Для получения самых последних обновлений соответствия, посетите [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) и поиск "Медиа-услуг", чтобы увидеть, если он соответствует сертификату интересов.|
|"Бесплатная пробная версия"|Восточная часть США|Недоступно|
|Доступность по регионам|Посмотреть [доступность когнитивных услуг по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Просмотрите [доступность медиа-услуг по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Дальнейшие действия

[Общие сведения об Индексаторе видео](video-indexer-overview.md)

[Что такое Службы мультимедиа Azure версии 3?](../latest/media-services-overview.md)
