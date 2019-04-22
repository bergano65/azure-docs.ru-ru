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
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270342"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Сравнение предустановок Служб мультимедиа версии 3 и Индексатора видео 

В этой статье сравниваются возможности **API Индексатора видео** и **API Служб мультимедиа (версия 3)**. 

В настоящее время имеется перекрытия функций, предоставляемых [API индексатора видео](https://api-portal.videoindexer.ai/) и [API-интерфейсы служб мультимедиа v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). В следующей таблице представлены сведения о текущих различиях и сходстве. 

## <a name="compare"></a>Сравнение

|Функция|API Индексатора видео |Предустановки анализатора видео и звука<br/>в Службах мультимедиа Azure, версия 3|
|---|---|---|
|Аналитические сведения о мультимедиа|[Расширенный](video-indexer-output-json-v2.md) |[Основы](../latest/intelligence-concept.md)|
|Возможности|Ознакомьтесь с полным списком поддерживаемых возможностей в следующей статье: <br/> [Обзор](video-indexer-overview.md)|Возвращает только аналитические сведения о видео|
|Выставление счетов|[Цены на Службы мультимедиа](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Цены на Службы мультимедиа](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Соответствие нормативным требованиям|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)и [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certified. Самые последние обновления, см. в статье [текущее состояние сертификации индексатора видео](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Службы мультимедиа соответствует требованиям многих сертификатов. Ознакомьтесь с документом [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) (Предложение для соответствия требованиям Azure) и просмотрите сведения о Службах мультимедиа, чтобы узнать, соответствуют ли они требованиям необходимого сертификата.|
|"Бесплатная пробная версия"|Восточная часть США|Недоступно|
|Регионы доступности|Восточная часть США 2, Юго-центральный регион США, Западная часть США 2, Северная Европа, Западная Европа, Юго-Восточная Азия, Восточная Азия и Восточная Австралия.  Самые последние обновления, см. в статье [продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) страницы.|Ознакомьтесь со статьей [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Дальнейшие действия

[Общие сведения об Индексаторе видео](video-indexer-overview.md)

[Что такое Службы мультимедиа Azure версии 3?](../latest/media-services-overview.md)
