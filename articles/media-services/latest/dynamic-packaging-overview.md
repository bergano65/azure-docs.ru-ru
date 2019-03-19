---
title: Обзор динамической упаковки служб мультимедиа Azure | Документация Майкрософт
description: В этой статье представлены общие сведения о технологии динамической упаковки в службах мультимедиа.
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
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: d9c59bdb2e8a7b115761554f70ebedeecaf5d04e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901747"
---
# <a name="dynamic-packaging"></a>Динамическая упаковка

Службы мультимедиа Microsoft Azure можно использовать для передачи многих файлов форматов мультимедиа, форматов потоков мультимедиа и форматов защищенного содержимого в клиенты различных технологий (например, iOS и XBOX). Эти клиенты работают по разным протоколам, например, для iOS используется формат потоковой трансляции HTTP (HLS), а для технологии Xbox необходимо использовать формат Smooth Streaming. Если у вас есть набор с переменной скоростью (с несколькими скоростями) MP4 (ISO Base Media 14496-12) файлы или набор файлов Smooth Streaming с переменной скоростью, которые вы хотите предоставить клиентам, поддерживающим HLS, MPEG DASH или Smooth Streaming, можно воспользоваться преимуществами динамической Упаковка. Упаковка не зависит от разрешения экрана, SD и HD/UHD - 4K поддерживаются.

[Конечные точки потоковой передачи](streaming-endpoint-concept.md) — это служба динамической упаковкой в службах мультимедиа, используемые для доставки содержимого мультимедиа клиента игроков. Динамическое упаковывание — это компонент, который поставляется на всех **конечными точками потоковой передачи** ("стандартный" или "премиум"). Нет без дополнительной затраты, связанные с этой возможности в версии 3 службы мультимедиа. 

Чтобы воспользоваться преимуществами **динамической упаковки**, необходимо иметь **активов** с набор мультискоростных MP4-файлов и потоковой передачи файлов конфигурации, необходимые для динамической упаковки служб мультимедиа. Для получения файлов рекомендуется кодировать мезонинный (исходный) файл с помощью служб мультимедиа. Чтобы сделать видео в закодированный ресурс доступным для клиентов для воспроизведения, то необходимо создать **указатель потоковой передачи** и построения потоковой передачи URL-адреса. Затем исходя из формата, указанного в манифест потоковой передачи клиента (HLS, DASH или Smooth), вы получаете потока на протокол, который вы выбрали.

В результате вы сможете хранить и оплачивать файлы только в одном формате, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента. 

В службах мультимедиа динамической упаковки, используется ли потоковая передача прямом эфире или по запросу. На следующей схеме показана потоковой передачи по требованию с помощью динамической упаковки рабочего процесса.

![Динамическая упаковка](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Общий рабочий процесс видео по запросу

Вот распространенные потоковой передачи рабочего процесса, в которых используется динамическая упаковка служб мультимедиа.

1. Отправьте входной файл (он также называется мезонинным). Например, это может быть файл в формате H.264, MP4 или WMV (список поддерживаемых форматов см. в статье [Форматы и кодеки стандартного кодировщика служб мультимедиа](media-encoder-standard-formats.md)).
2. Преобразуйте мезонинный файл в набор мультискоростных MP4-файлов в формате H.264.
3. Опубликуйте ресурс,который содержит набор MP4-файлов с адаптивной скоростью. Опубликовать, создав **указатель потоковой передачи**.
4. Создание URL-адресов, предназначенных для различных форматах (HLS, Dash и Smooth Streaming). **Конечной точки потоковой передачи** бы берем на себя обслуживающий правильный манифест и запросы для всех этих форматов.

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Кодирование с адаптивной скоростью MP4-файлов

Сведения о [способы кодирования видео с помощью служб мультимедиа](encoding-concept.md), см. в следующих примерах:

* [Кодирование из URL-адрес HTTPS, используя встроенные стили](job-input-from-http-how-to.md)
* [Кодирование локальный файл, используя встроенные стили](job-input-from-local-file-how-to.md)
* [Создать пользовательскую предустановку для конкретных требований сценария или устройства](customize-encoder-presets-how-to.md)

Список кодеков и форматов стандартного кодировщика мультимедиа, см. в разделе [форматы и кодеки](media-encoder-standard-formats.md)

## <a name="delivery-protocols"></a>Протоколы доставки

|Протокол|Пример|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Видеокодеки поддерживаются динамической упаковкой

Динамическое упаковывание поддерживает MP4-файлов, которые содержат видео, закодированное с [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC или AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 или hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Аудиокодеки, поддерживаемые для динамической упаковки

Динамическое упаковывание поддерживает MP4-файлов, которые содержат аудио, закодированные с [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 или E-AC3), или [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR служб DTS, HD служб DTS, HD служб DTS без потери данных).

> [!NOTE]
> Для динамической упаковки не поддерживаются файлы с аудиоданными в формате [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (это устаревший кодек).

## <a name="manifests"></a>Манифесты 
 
Службы мультимедиа поддерживают протоколы Smooth Streaming, MPEG DASH, HLS. Как часть **динамической упаковки**, потоковой передачи клиентских манифестов (списка воспроизведения HLS Master, описание презентации мультимедиа DASH (MPD) и Smooth Streaming) создаются динамически в зависимости от выбора формата в URL-адрес. См. в разделе протоколы доставки в [в этом разделе](#delivery-protocols). 

Файл манифеста содержит потоковые метаданные, такие как: отслеживание типа (аудио, видео или текст), отслеживать, имя, время начала и окончания, скорость (качество), языки дорожки, окно представления (скользящее окно фиксированной длительности), видеокодек (FourCC). Также он предписывает проигрывателю получить следующий фрагмент, предоставляя информацию о следующих доступных для воспроизведения фрагментах видео и их расположении. Фрагменты (или сегменты) — это фактические блоки видеосодержимого.

### <a name="hls-master-playlist"></a>Список воспроизведения HLS Master

Ниже приведен пример файла манифеста HLS. 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>Описание презентации мультимедиа DASH (MPD)

Ниже приведен пример манифеста DASH:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Ниже приведен пример манифеста Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```
## <a name="next-steps"></a>Дальнейшие действия

[Отправка, кодирование и потоковая передача видео](stream-files-tutorial-with-api.md)

