---
title: Форматы и кодеки Media Encoder Standard — Azure
description: В этой статье приводятся общие сведения о форматах Media Encoder Standard и кодеках.
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
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: e2ccce13e4ef09426d0f3a02dcbce2f330b0ead8
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895931"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Форматы и кодеки Media Encoder Standard

> [!div class="op_single_selector" title1="Выберите версию служб мультимедиа, которую вы используете:"]
> * [Версия 2](media-services-media-encoder-standard-formats.md)
> * [Версия 3](../latest/media-encoder-standard-formats.md)

Этот документ содержит список наиболее распространенных форматов для импорта и экспорта файлов, которые можно использовать со стандартным кодировщиком служб мультимедиа.

## <a name="input-containerfile-formats"></a>Контейнер ввода и форматы файлов
| Форматы файлов (расширения файлов) | Поддерживается |
| --- | --- |
| FLV (с кодеками H.264 и AAC) (.flv) |ДА |
| MXF (.mxf) |ДА |
| GXF (.gxf) |ДА |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |ДА |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |ДА |
| AVI (без сжатия 8 бит/10 бит) (.avi) |ДА |
| MP4 (MP4, M4A, M4V) и ISMV (ISMA, ISMV) |ДА |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |ДА |
| Matroska/WebM (.mkv) |ДА |
| WAVE/WAV (.wav) |ДА |
| QuickTime (.mov) |ДА |

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
| Входные видеокодеки | Поддерживается |
| --- | --- |
| AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 |
| Avid DNxHD (в MXF) |ДА |
| DVCPro/DVCProHD (в MXF) |ДА |
| Цифровое видео (DV) (в AVI-файлах) |ДА |
| JPEG 2000 |ДА |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |До 422 Profile |
| MPEG-1 |ДА |
| VC-1/WMV9 |ДА |
| Canopus HQ/HQX |Нет |
| MPEG-4, часть 2 |ДА |
| [Theora](https://en.wikipedia.org/wiki/Theora) |ДА |
| YUV420 без сжатия или мезонинный файл |ДА |
| Apple ProRes 422 |ДА |
| Apple ProRes 422 LT |ДА |
| Apple ProRes 422 HQ |ДА |
| Apple ProRes Proxy |ДА |
| Apple ProRes 4444 |ДА |
| Apple ProRes 4444 XQ |ДА |
| HEVC/H.265| Профиль Main и Main 10 (&#42;)<br/>Поддержка профиля Main 10 предназначена для 8-битного содержимого в формате 4:2:0. |

## <a name="input-audio-codecs"></a>Входные аудиокодеки
| Входные аудиокодеки | Поддерживается |
| --- | --- |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |ДА |
| MPEG Layer 2 |ДА |
| MP3 (MPEG-1 Audio Layer 3) |ДА |
| Windows Media Audio |ДА |
| WAV/PCM |ДА |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |ДА |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |ДА |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |ДА |
| AMR (adaptive multi-rate) |ДА |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет |
| Dolby® E |Нет |
| Dolby® Digital (AC3) |Нет |
| Dolby® Digital Plus (E-AC3) |Нет |

## <a name="output-formats-and-codecs"></a>Выходные форматы и кодеки
В следующей таблице перечислены кодеки и форматы файлов, поддерживаемые для экспорта.

| Формат файла | Видеокодек | Аудиокодек |
| --- | --- | --- |
| MP4 <br/><br/>(включая контейнеры MP4 с несколькими скоростями) |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отправить отзыв
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Дополнительные материалы
[Кодирование содержимого по запросу с помощью служб мультимедиа Azure](media-services-encode-asset.md)

[Кодирование с помощью стандартного кодировщика мультимедиа](media-services-dotnet-encode-with-media-encoder-standard.md)

