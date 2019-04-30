---
title: Форматы и кодеки Media Encoder Standard — Azure
description: В этом разделе приведен обзор кодеков и форматов стандартного кодировщика мультимедиа.
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
ms.date: 03/18/2019
ms.author: juliako;anilmur
ms.openlocfilehash: c862de2eec4e6c116218457a20b567dc02778685
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463741"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Форматы и кодеки стандартного кодировщика служб мультимедиа

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Версия 2](media-services-media-encoder-standard-formats.md)
> * [Версия 3](../latest/media-encoder-standard-formats.md)

Этот документ содержит список наиболее распространенных форматов для импорта и экспорта файлов, которые можно использовать со стандартным кодировщиком служб мультимедиа.

## <a name="input-containerfile-formats"></a>Контейнер ввода и форматы файлов
| Форматы файлов (расширения файлов) | Поддерживаются |
| --- | --- |
| FLV (с кодеками H.264 и AAC) (.flv) |Да |
| MXF (.mxf) |Да |
| GXF (.gxf) |Да |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Да |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Да |
| AVI (без сжатия 8 бит/10 бит) (.avi) |Да |
| MP4 (MP4, M4A, M4V) и ISMV (ISMA, ISMV) |Да |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Да |
| Matroska/WebM (.mkv) |Да |
| WAVE/WAV (.wav) |Да |
| QuickTime (.mov) |Да |

> [!NOTE]
> Выше приведен список чаще всего встречающихся файловых расширений. Стандартный кодировщик служб мультимедиа поддерживает многие другие стандарты (например, M2TS, MPEG2VIDEO, QT). Если при попытке закодировать файл появляется сообщение о том, что формат не поддерживается, оставьте свой отзыв [здесь](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Форматы аудио во входных контейнерах
Стандарт Media Encoder поддерживает следующие форматы аудио во входных контейнерах:

* файлы MXF, GXF и QuickTime со звуковыми дорожками с чередованием стерео или форматом 5.1;

или

* файлы MXF, GXF и QuickTime, где звук переносится как отдельные дорожки PCM, но сопоставление каналов (стерео или 5.1) можно определить по метаданным файла.

## <a name="input-video-codecs"></a>Входные видеокодеки
| Входные видеокодеки | Поддерживаются |
| --- | --- |
| AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 |
| Avid DNxHD (в MXF) |Да |
| DVCPro/DVCProHD (в MXF) |Да |
| Цифровое видео (DV) (в AVI-файлах) |Да |
| JPEG 2000 |Да |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |До 422 Profile |
| MPEG-1 |Да |
| VC-1/WMV9 |Да |
| Canopus HQ/HQX |Нет |
| MPEG-4, часть 2 |Да |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Да |
| YUV420 без сжатия или мезонинный файл |Да |
| Apple ProRes 422 |Да |
| Apple ProRes 422 LT |Да |
| Apple ProRes 422 HQ |Да |
| Apple ProRes Proxy |Да |
| Apple ProRes 4444 |Да |
| Apple ProRes 4444 XQ |Да |
| HEVC/H.265| Профиль Main и Main 10 (&#42;)<br/>Поддержка профиля Main 10 предназначена для 8-битного содержимого в формате 4:2:0. |

## <a name="input-audio-codecs"></a>Входные аудиокодеки
| Входные аудиокодеки | Поддерживаются |
| --- | --- |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Да |
| MPEG Layer 2 |Да |
| MP3 (MPEG-1 Audio Layer 3) |Да |
| Windows Media Audio |Да |
| WAV/PCM |Да |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Да |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Да |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Да |
| AMR (adaptive multi-rate) |Да |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет |
| Dolby® E |Нет |
| Dolby® Digital (AC3) |Нет |
| Dolby® Digital Plus (E-AC3) |Нет |

## <a name="output-formats-and-codecs"></a>Выходные форматы и кодеки
В следующей таблице перечислены кодеки и форматы файлов, поддерживаемые для экспорта.

| Формат файла | Видеокодек | Аудиокодек |
| --- | --- | --- |
| MP4  <br/><br/>(включая контейнеры MP4 с несколькими скоростями) |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>См. также
[Кодирование содержимого по запросу с помощью служб мультимедиа Azure](media-services-encode-asset.md)

[Кодирование с помощью стандартного кодировщика мультимедиа](media-services-dotnet-encode-with-media-encoder-standard.md)

