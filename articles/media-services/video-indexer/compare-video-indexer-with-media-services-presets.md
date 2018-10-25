---
title: Сравнение Индексатора видео и предустановок Служб мультимедиа (версия 3) | Документация Майкрософт
description: В этой статье Индексатор видео сравнивается с предустановками Служб мультимедиа версии 3.
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
ms.date: 10/03/2018
ms.author: juliako
ms.openlocfilehash: e9b66721135456320134f41c614b194d1e22b10f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380789"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Сравнение предустановок Служб мультимедиа версии 3 и Индексатора видео 

В этой статье сравниваются возможности **API Индексатора видео** и **API Служб мультимедиа (версия 3)**. 

В настоящее время в [API Индексатора видео версии 2](https://api-portal.videoindexer.ai/) и [API Служб мультимедиа версии 3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) некоторые функции одинаковые. В следующей таблице представлены сведения о текущих различиях и сходстве. 

## <a name="compare"></a>Сравнение

|Функция|API Индексатора видео |Предустановки анализатора видео и звука<br/>в Службах мультимедиа Azure, версия 3|
|---|---|---|
|Аналитические сведения о мультимедиа|[Расширенный](../../cognitive-services/video-indexer/video-indexer-output-json-v2.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json) |[Основы](../latest/intelligence-concept.md)|
|Возможности|Ознакомьтесь с полным списком поддерживаемых возможностей в следующей статье: <br/> [Обзор](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)|Возвращает только аналитические сведения о видео|
|Выставление счетов|[Цены на Службы мультимедиа](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Цены на Службы мультимедиа](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Соответствие нормативным требованиям|ПОДЛЕЖИТ УТОЧНЕНИЮ|Службы мультимедиа соответствует требованиям многих сертификатов. Ознакомьтесь с документом [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) (Предложение для соответствия требованиям Azure) и просмотрите сведения о Службах мультимедиа, чтобы узнать, соответствуют ли они требованиям необходимого сертификата.|
|"Бесплатная пробная версия"|Восточная часть США|Недоступно|
|Доступность |Западная часть США, Восточная Азия, Северная Европа|Ознакомьтесь со статьей [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Дополнительная информация

[Общие сведения об Индексаторе видео](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)

[Что такое Службы мультимедиа Azure версии 3?](../../media-services/latest/media-services-overview.md)
