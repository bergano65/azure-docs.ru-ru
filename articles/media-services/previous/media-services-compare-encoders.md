---
title: Сравнение кодировщиков мультимедиа Azure по запросу | Документация Майкрософт
description: В этом разделе сравниваются возможности кодирования **Media Encoder Standard** и **рабочего процесса Media Encoder Premium**.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016657"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Сравнение кодировщиков мультимедиа Azure по запросу  

В этом разделе сравниваются возможности кодирования **Media Encoder Standard** и **рабочего процесса Media Encoder Premium**.

## <a name="video-and-audio-processing-capabilities"></a>Возможности обработки видео и звука

В следующей таблице сравниваются функциональные возможности Media Encoder Standard (MES) и Media Encoder Premium Workflow (MEPW). 

|Функция|Стандартный кодировщик служб мультимедиа|Media Encoder Premium Workflow|
|---|---|---|
|Применение условной логики при кодировании<br/>(например, если входные данные в формате HD, то следует кодировать звук в формат 5.1)|нет|Да|
|Субтитры стандарта|нет|[Да](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> с Dialogue Intelligence™|нет|Да|
|Устранение чересстрочности, обратное преобразование видео|Basic|Качество трансляции|
|Обнаружение и удаление черных границ <br/>(вертикальные и горизонтальные рамки)|нет|Да|
|Создание эскизов|[Да](media-services-dotnet-generate-thumbnail-with-mes.md)|[Да](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Обрезание и совмещение видео|[Да](media-services-advanced-encoding-with-mes.md#trim_video)|Да|
|Наложение звука или видео|[Да](media-services-advanced-encoding-with-mes.md#overlay)|[Да](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Наложение изображений|Из источников изображений|Из источников изображений и текста|
|Несколько аудиодорожек на разных языках|Ограничено|[Да](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Тарифные единицы, используемые кодировщиками
| Имя обработчика мультимедиа | Применимые цены | Примечания |
| --- | --- | --- |
| **Стандартный кодировщик служб мультимедиа** |КОДИРОВЩИК |Плата за выполнение задач кодирования взимается на основе общей продолжительности (в минутах) всех файлов мультимедиа, созданных в качестве выходных файлов, по тарифу, указанному [здесь][1] в столбце "КОДИРОВЩИК". |
| **Media Encoder Premium Workflow** |РАСШИРЕННЫЙ КОДИРОВЩИК |Плата за выполнение задач кодирования взимается на основе общей продолжительности (в минутах) всех файлов мультимедиа, созданных в качестве выходных файлов, по тарифу, указанному [здесь][1] в столбце PREMIUM ENCODER (Кодировщик ценовой категории "Премиум"). |

## <a name="input-containerfile-formats"></a>Контейнер ввода и форматы файлов
| Контейнер ввода/ форматы файлов | Стандартный кодировщик служб мультимедиа | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |Да |Да |
| MXF/SMPTE 377M |Да |Да |
| GXF |Да |Да |
| Транспортные потоки MPEG-2 |Да |Да |
| Программные потоки MPEG-2 |Да |Да |
| MPEG-4/MP4 |Да |Да |
| Windows Media/ASF |Да |Да |
| AVI (без сжатия 8 бит/10 бит) |Да |Да |
| 3GPP/3GPP2 |Да |нет |
| Формат файлов Smooth Streaming (PIFF 1.3) |Да |нет |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Да |нет |
| Matroska/WebM |Да |нет |
| QuickTime (.mov) |Да |нет |

## <a name="input-video-codecs"></a>Входные видеокодеки
| Входные видеокодеки | Стандартный кодировщик служб мультимедиа | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 |Да |
| Avid DNxHD (в MXF) |Да |Да |
| DVCPro/DVCProHD (в MXF) |Да |Да |
| JPEG2000 |Да |Да |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |До 422 Profile |Да |
| MPEG-1 |Да |Да |
| Windows Media Video/VC-1 |Да |Да |
| Canopus HQ/HQX |нет |нет |
| MPEG-4, часть 2 |Да |нет |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Да |нет |
| Apple ProRes 422 |Да |нет |
| Apple ProRes 422 LT |Да |нет |
| Apple ProRes 422 HQ |Да |нет |
| Apple ProRes Proxy |Да |нет |
| Apple ProRes 4444 |Да |нет |
| Apple ProRes 4444 XQ |Да |нет |
| HEVC/H.265|Профиль Main|Профиль Main и Main 10|

## <a name="input-audio-codecs"></a>Входные аудиокодеки
| Входные аудиокодеки | Стандартный кодировщик служб мультимедиа | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M и 302M, AES3-2003) |нет |Да |
| Dolby® E |нет |Да |
| Dolby® Digital (AC3) |нет |Да |
| Dolby® Digital Plus (E-AC3) |нет |Да |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Да |Да |
| MPEG Layer 2 |Да |Да |
| MP3 (MPEG-1 Audio Layer 3) |Да |Да |
| Windows Media Audio |Да |Да |
| WAV/PCM |Да |Да |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Да |нет |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Да |нет |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Да |нет |

## <a name="output-containerfile-formats"></a>Контейнер вывода и форматы файлов
| Контейнер вывода/ форматы файлов | Стандартный кодировщик служб мультимедиа | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |нет |Да |
| MXF (OP1a, XDCAM и AS02) |нет |Да |
| DPP (включая AS11) |нет |Да |
| GXF |нет |Да |
| MPEG-4/MP4 |Да |Да |
| MPEG-TS |Да |Да |
| Windows Media/ASF |нет |Да |
| AVI (без сжатия 8 бит/10 бит) |нет |Да |
| Формат файлов Smooth Streaming (PIFF 1.3) |нет |Да |

## <a name="output-video-codecs"></a>Выходные видеокодеки
| Выходные видеокодеки | Стандартный кодировщик служб мультимедиа | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC (H.264; 8-разрядный; до High Profile, Level 5.2; 4K Ultra HD; AVC Intra) |Только 8 бит 4:2:0 |Да |
| HEVC (H.265; 8-разрядный и 10-разрядный)  |нет |Да |
| Avid DNxHD (в MXF) |нет |Да |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |нет |Да |
| MPEG-1 |нет |Да |
| Windows Media Video/VC-1 |нет |Да |
| Создание эскизов JPEG |Да |Да |
| Создание эскизов PNG |Да |Да |
| Создание эскизов BMP |Да |нет |

## <a name="output-audio-codecs"></a>Выходные аудиокодеки
| Выходные аудиокодеки | Стандартный кодировщик служб мультимедиа | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M и 302M, AES3-2003) |нет |Да |
| Dolby® Digital (AC3) |нет |Да |
| Dolby® Digital Plus (E-AC3) до 7.1 |нет |Да |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Да |Да |
| MPEG Layer 2 |нет |Да |
| MP3 (MPEG-1 Audio Layer 3) |нет |Да |
| Windows Media Audio |нет |Да |

>[!NOTE]
>При кодировании в формат Dolby® Digital (AC3) выходные данные могут записываться только в MP4-файл ISO.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Связанные статьи
* [Выполняйте расширенные задачи кодирования путем настройки предустановок стандартного кодировщика служб мультимедиа](media-services-custom-mes-presets-with-dotnet.md)
* [Квоты и ограничения](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
