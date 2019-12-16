---
title: Динамическая упаковка в Службах мультимедиа
titleSuffix: Azure Media Services
description: В этой статье представлены общие сведения о технологии динамической упаковки в Службах мультимедиа Azure.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: c12ebe307eb370e07b785b4379f82a4274964492
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977766"
---
# <a name="dynamic-packaging-in-media-services"></a>Динамическая упаковка в Службах мультимедиа

Службы мультимедиа Microsoft Azure можно использовать для кодирования исходных файлов мультимедиа во множество форматов. Они предоставляют их через различные протоколы потоковой передачи (с защитой содержимого или без нее) для доступа к устройствам всех основных типов (например, на платформах с iOS и Android). Эти клиенты распознают разные протоколы. Например, для iOS потоки должны доставляться в формате HTTP Live Streaming (HLS), а устройства Android поддерживают как HLS, так и формат MPEG DASH.

В Службах мультимедиа [конечная точка потоковой передачи](streaming-endpoint-concept.md) — это служба динамической (JIT) упаковки и исходная служба, которая может доставить содержимое в реальном времени и по запросу непосредственно в клиентское приложение проигрывателя. Она использует один из распространенных протоколов потоковой передачи мультимедиа, упомянутых в следующем разделе. Динамическая упаковка — это функция, которая по умолчанию предоставляется во всех ценовых категориях ("Стандартный" или "Премиум") службы "Конечные точки потоковой передачи".

## <a name="a-iddelivery-protocolsto-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Подготовка исходных файлов к доставке

Чтобы воспользоваться преимуществами динамической упаковки, вам необходимо [кодировать](encoding-concept.md) свой мезонинный (исходный) файл в набор файлов MP4 (ISO Base Media 14496-12) с несколькими битрейтами. Вам нужен набор [Asset](assets-concept.md) с зашифрованными MP4 файлами и конфигурационными файлами потоковой передачи, необходимыми для динамической пакетной обработки мультимедийных служб. Из этого набора файлов MP4 вы можете использовать динамическую упаковку для передачи видео по следующим протоколам потоковой передачи мультимедиа:

|Протокол|Пример|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

Если вы планируете защитить содержимое с помощью динамического шифрования Media Services, см. раздел [Потоковые протоколы и типы шифрования](content-protection-overview.md#streaming-protocols-and-encryption-types).

> [!TIP]
> Один из способов получить MP4 и потоковые файлы конфигурации заключается в [кодировании вашего мезонинного файла с помощью Media Services](#encode-to-adaptive-bitrate-mp4s). 

Чтобы видео в закодированном ресурсе стали доступны для воспроизведения в клиентах, необходимо создать [указатель потоковой передачи](streaming-locators-concept.md) и сформировать URL-адреса потоковой передачи. Затем, в зависимости от формата, указанного в манифесте клиента потоковой передачи (HLS, MPEG DASH или Smooth Streaming), вы получаете поток по выбранному протоколу.

В результате вы сможете хранить и оплачивать файлы только в одном формате, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента.

## <a name="on-demand-streaming-workflow"></a>Рабочий процесс для потокового воспроизведения по запросу

Далее описаны этапы обычного рабочего процесса потоковой передачи в Службах мультимедиа, при котором динамическая упаковка используется с кодировщиком (цен. категория "Стандартный") в Службах мультимедиа Azure.

1. Отправка входного файла, например в формате QuickTime/MOV или MXF. Этот файл также называется мезонинным или исходным. Список поддерживаемых форматов см. в статье [Форматы и кодеки кодировщика служб мультимедиа (цен. категория "Стандартный")](media-encoder-standard-formats.md).
1. [Закодируйте](#encode-to-adaptive-bitrate-mp4s) мезонинный файл в набор MP4-файлов с адаптивной скоростью в формате H.264 или AAC.
1. Опубликуйте выходной ресурс, который содержит набор MP4-файлов с адаптивной скоростью. Вы выполняете публикацию путем создания указателя потоковой передачи.
1. Создайте URL-адреса, которые предназначены для различных форматов (HLS, MPEG-DASH и Smooth Streaming). **Конечная точка потоковой передачи** возьмет на себя обслуживание надлежащего манифеста и запросов для всех этих различных форматов.

На приведенной ниже схеме показан рабочий процесс для потокового воспроизведения по запросу с динамической упаковкой.

![Схема рабочего процесса для потокового воспроизведения по запросу с динамической упаковкой](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Кодирование в MP4-файлы с адаптивной скоростью

В ресурсах по указанным ниже ссылкам показаны примеры [кодирования видео с помощью Служб мультимедиа](encoding-concept.md):

* [Кодирование из URL-адреса HTTPS с использованием встроенных предустановок](job-input-from-http-how-to.md).
* [Кодирование локального файла с использованием встроенных предустановок](job-input-from-local-file-how-to.md).
* [Создание пользовательской предустановки в соответствии с требованиями сценария или устройства](customize-encoder-presets-how-to.md).

Просмотрите список [форматов и кодеков](media-encoder-standard-formats.md) Media Encoder Standard.

## <a name="live-streaming-workflow"></a>Рабочий процесс для потоковой трансляции в реальном времени

Есть два типа событий потоковой трансляции: сквозной режим и кодирование в реальном времени. 

Ниже описан распространенный рабочий процесс для потоковой трансляции с динамической упаковкой.

1. Создайте [событие прямой трансляции](live-events-outputs-concept.md).
1. Получите URL-адрес приема и настройте использование URL-адреса в локальном кодировщике для отправки веб-канала входного потока.
1. Получите URL-адрес для предварительного просмотра, чтобы проверить получение входных данных от кодировщика.
1. Создайте ресурс.
1. Создайте компонент выходных данных прямой трансляции с использованием имени созданного ресурса.<br />Выходные данные потоковой трансляции архивируют поток в ресурс.
1. Создайте указатель потоковой передачи со встроенным типом политики потоковой передачи.<br />Если вы хотите зашифровать свое содержимое, ознакомьтесь с [обзором системы защиты содержимого](content-protection-overview.md).
1. Создайте список путей на основе указателя потоковой передачи, чтобы получать URL-адреса для использования.
1. Получите имя узла для конечной точки потоковой передачи, с которой должна идти потоковая передача.
1. Создайте URL-адреса, которые предназначены для различных форматов (HLS, MPEG-DASH и Smooth Streaming). Конечная точка потоковой передачи берет на себя обслуживание правильного манифеста и запросов для различных форматов.

На схеме показан рабочий процесс для потоковой трансляции с динамической упаковкой.

![Схема рабочего процесса для сквозного кодирования с динамической упаковкой](./media/live-streaming/pass-through.svg)

Сведения о потоковой трансляции в Службах мультимедиа версии 3 см. в статье [здесь](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Видеокодеки, поддерживаемые для динамической упаковки

Динамическая упаковка поддерживает MP4-файлы, которые содержат видео, закодированное с помощью [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC или AVC1) или [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 или hvc1).

> [!NOTE]
> С динамической упаковкой было протестировано видео с разрешением до 4K и частотой до 60 кадров в секунду. [Кодировщик ценовой категории "Премиум"](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) поддерживает кодирование в формат H.265 с помощью устаревших API версии 2.

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Аудиокодеки, поддерживаемые для динамической упаковки

Динамическая упаковка поддерживает MP4-файлы с аудиоданными, закодированными с помощью следующих протоколов:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 или HE-AAC v2).
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 или E-AC3).
* Dolby Atmos<br />
   Потоковая передача содержимого Dolby Atmos поддерживается такими стандартами, как протокол MPEG-DASH с форматом потоковой передачи Common Streaming Format (CSF) или форматом Common Media Application Format (CMAF) фрагментированного MP4, а также через HTTP Live Streaming (HLS) со CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Кодеки DTS, поддерживаемые форматами упаковки DASH-CSF, DASH-CMAF, HLS-M2TS и HLS-CMAF:  

    * DTS Digital Surround (dtsc);
    * DTS-HD High Resolution и DTS-HD Master Audio (dtsh);
    * DTS Express (dtse);
    * DTS-HD Lossless (no core) (dtsl).

Динамическая упаковка поддерживает несколько звуковых дорожек вывода с DASH или HLS (версии 4 или более поздней) для потоковой передачи ресурсов, у которых несколько звуковых дорожек с поддержкой нескольких кодеков и языков.

### <a name="additional-notes"></a>Дополнительные замечания

Динамическая упаковка не поддерживает файлы с аудиоданными в формате [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (это устаревший кодек).

> [!NOTE]
> [Кодировщик ценовой категории "Премиум"](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) поддерживает кодирование в формат Dolby Digital Plus с помощью устаревших API версии 2.

## <a name="manifests"></a>Манифесты

В динамической упаковке Служб мультимедиа манифесты клиентов потоковой передачи для HLS, MPEG-DASH и Smooth Streaming динамически создаются на основе селектора формата в URL-адресе.  

Файл манифеста содержит потоковые метаданные, например тип дорожки (звук, видео или текст), имя дорожки, время начала и окончания, скорость (качество), языки дорожки, окно представления (скользящее окно фиксированной длительности), видеокодек (FourCC). Кроме того, он предписывает проигрывателю получить следующий фрагмент, предоставляя информацию о следующих доступных для воспроизведения фрагментах видео и их расположении. Фрагменты (или сегменты) — это фактические блоки видеосодержимого.

### <a name="examples"></a>Примеры

#### <a name="hls"></a>HLS

Ниже приведен пример файла манифеста HLS, также называемого главным списком воспроизведения HLS: 

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

#### <a name="mpeg-dash"></a>MPEG-DASH

Ниже приведен пример файла манифеста MPEG-DASH, также называемого форматом описания представления мультимедиа (MPD) MPEG-DASH:

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
#### <a name="smooth-streaming"></a>Smooth Streaming

Ниже приведен пример файла манифеста Smooth Streaming:

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

### <a name="naming-of-tracks-in-the-manifest"></a>Именование дорожек в манифесте

Если в ISM-файле указано имя звуковой дорожки, Службы мультимедиа добавляют элемент `Label` в `AdaptationSet`, чтобы указать структурные сведения для конкретной звуковой дорожки. Пример выходного манифеста DASH:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Проигрыватель может использовать элемент `Label` для отображения в пользовательском интерфейсе.

### <a name="signaling-audio-description-tracks"></a>Сигнальные звуковые описания дорожек

Вы можете добавить в видео дорожку речевого сопровождения, чтобы помочь слабовидящим клиентам следить за видеозаписью, слушая повествование. Вам необходимо добавлять заметки к звуковой дорожке в качестве звукового описания в манифесте. Для этого добавьте в файл .ism. параметры "специальные возможности" и "роль". Вы должны правильно задать эти параметры, чтобы звуковая дорожка передавалась в качестве звукового описания. Например, добавьте в файл .ism `<param name="accessibility" value="description" />` и `<param name="role" value="alternate"` для определенной звуковой дорожки. 

Дополнительные сведения см. в примере [Как добавить сигналы к описанию звуковой дорожки](signal-descriptive-audio-howto.md).

#### <a name="smooth-streaming-manifest"></a>Манифест Smooth Streaming

При воспроизведении потоковой передачи Smooth Streaming для этой звуковой дорожки манифест будет содержать значения в атрибутах `Accessibility` и `Role`. Например, `Role="alternate" Accessibility="description"` будет добавлен в элемент `StreamIndex`, чтобы указать, что это звуковое описание.

#### <a name="dash-manifest"></a>Манифест DASH

Чтобы сигнализировать о звуковом описании, для манифеста DASH будут добавлены следующие два элемента:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Список воспроизведения HLS

Для HLS версии 7 и выше `(format=m3u8-cmaf)` при получении сигнала для дорожки звукового описания список воспроизведения будет содержать `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"`.

#### <a name="example"></a>Пример

Дополнительные сведения см. в разделе [Как сигнализировать об описании звуковых дорожек](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Динамический манифест

Чтобы контролировать количество дорожек, форматы, скорость и окно времени презентации для передачи на проигрыватели, вы можете использовать динамическую фильтрацию с помощью динамического упаковщика Служб мультимедиа. Дополнительные сведения см. в статье [Pre-filtering manifests with Dynamic Packager](filters-dynamic-manifest-overview.md) (Предварительная фильтрация манифестов с помощью динамического упаковщика).

## <a name="dynamic-encryption"></a>Динамическое шифрование

Вы можете использовать *динамическое шифрование*, чтобы динамически шифровать содержимое в режиме реального времени или по требованию с помощью AES-128 или трех основных систем управления цифровыми правами (DRM): Microsoft PlayReady, Google Widevine и Apple FairPlay. Авторизованным клиентам также предоставляется служба доставки ключей AES и лицензий DRM. Чтобы узнать больше, ознакомьтесь со статьей [о динамическом шифровании](content-protection-overview.md).

> [!NOTE]
> Widevine — это служба, которая предоставляется компанией Google Inc. и подпадает под условия предоставления услуг и политику конфиденциальности Google Inc.

## <a name="more-information"></a>Дополнительные сведения

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="need-help"></a>Требуется помощь?

Вы можете открыть запрос в службу поддержки, перейдя к разделу [нового запроса на техническую поддержку](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Дополнительная информация

> [!NOTE]
> В настоящее время вы не можете использовать портал Azure для управления ресурсами версии 3. Используйте [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) или один из поддерживаемых [пакетов SDK](media-services-apis-overview.md#sdks).

[Руководство. Отправка, кодирование и потоковая передача видео](stream-files-tutorial-with-api.md).
