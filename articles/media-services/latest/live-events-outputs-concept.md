---
title: Концепции живых событий и выходов в прямом эфире в Azure Media Services v3
titleSuffix: Azure Media Services
description: Эта тема содержит обзор живых событий и живых выходов в Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/08/2020
ms.author: juliako
ms.openlocfilehash: 8a00f7c0ec76510cc521966acf98b7250e723697
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985904"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>События в прямом эфире и живые выходы в медиа-услугах

Медиа-службы Azure позволяют предоставлять своим клиентам живые события в облаке Azure. Чтобы настроить события потоковой передачи в Media Services v3, необходимо понять концепции, обсуждаемые в этой статье.

> [!TIP]
> Для клиентов, мигрирующих из AIS Media Services v2, организация **Live Event** заменяет **канал** в v2, а **Live Output** заменяет **программу.**

## <a name="live-events"></a>Динамические события

[События потоковой трансляции](https://docs.microsoft.com/rest/api/media/liveevents) отвечают за прием и обработку видеопотоков. При создании Live Event создается первичная и второстепенная конечная точка ввода, которую можно использовать для отправки живого сигнала с удаленного кодера. Удаленный живой кодер отправляет канал вклада в эту конечную точку ввода, используя либо протокол ввода [RTMP](https://www.adobe.com/devnet/rtmp.html) или [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (фрагментированный MP4). Для протокола RTMP глотать, содержание можно`rtmp://`послать в ясном ()`rtmps://`или безопасно зашифровано на проводе(). Для протокола приема Smooth Streaming поддерживаются следующие схемы URL-адресов: `http://` или `https://`.  

## <a name="live-event-types"></a>Типы событий потоковой трансляции

[Live Event](https://docs.microsoft.com/rest/api/media/liveevents) может быть установлен либо *сквозной* (на месте живой кодер посылает несколько битрат поток) или *живой кодирования* (на месте живой кодер посылает один битрат поток). Типы устанавливаются во время создания с помощью [LiveEventEncodingType:](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None**: На-предпосылки живой кодер посылает несколько битрат поток. Просеченный поток проходит через Live Event без дальнейшей обработки. Также называется сквозной режим.
* **LiveEventEncodingType.Standard**: В непредпомшенном live-кодере отправляется один битратный поток в Live Event, а медиа-сервисы создают несколько битратных потоков. Если канал вклада 720p или более высокое разрешение, preset **Default720p** закодирует набор из 6 пар разрешения/битратов.
* **LiveEventEncodingType.Premium1080p**: В помещении живой кодер отправляет один битрит поток Live Event и Медиа-услугсоздает несколько битратных потоков. Предустановленный набор Default1080p определяет набор выходных пар разрешения/битратов.

### <a name="pass-through"></a>Сквозной режим

![сквозное live-event с примером медиа-услуг диаграммы](./media/live-streaming/pass-through.svg)

При использовании сквозного режима **потоковой трансляции** локальный динамический кодировщик генерирует многоскоростной видеопоток и передает его как исходный в трансляцию с помощью протокола RTMP или фрагментированного MP4. Затем событие потоковой трансляции передает входящие видеопотоки без какой-либо дальнейшей обработки. Такое сквозное Live Event оптимизировано для длительных живых событий или 24x365 линейной потоковой передачи. Создавая событие потоковой трансляции такого типа, укажите значение None (LiveEventEncodingType.None).

При этом исходный поток передается с разрешением до 4K и частотой 60 кадров в секунду при помощи видеокодеков H.264/AVC или H.265/HEVC, а также аудиокодека AAC (AAC-LC, HE-AACv1 или HE-AACv2). Для получения дополнительной [Live Event types comparison](live-event-types-comparison.md)информации см.

> [!NOTE]
> Использование сквозного метода является наиболее экономичным способом сделать потоковое воспроизведение, когда вы делаете несколько событий в течение длительного периода времени, и вы уже инвестировали в антеповых кодеров. Ознакомьтесь с сведениями [о ценах.](https://azure.microsoft.com/pricing/details/media-services/)
>

См. пример кода .NET в [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Кодирование в реальном времени  

![кодирование в реальном времени с помощью диаграммы примера медиа-услуг](./media/live-streaming/live-encoding.svg)

При использовании живого кодирования с помощью Media Services вы настраиваете свой в он-презентатор для отправки одного битратного видео в качестве канала вклада в Live Event (с помощью протокола RTMP или Fragmented-Mp4). Затем вы настраиваете Live Event таким образом, чтобы оно кодирует входящий поток одного битрата в [поток мультибитного видео,](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)и делает выход доступным для доставки для воспроизведения устройств с помощью таких протоколов, как MPEG-DASH, HLS и Smooth Streaming.

При использовании живого кодирования, вы можете отправить канал вклада только в разрешении до 1080p разрешение при частоте кадров 30 кадров в секунду, с H.264/AVC видео кодек и AAC (AAC-LC, HE-AACv1, или HE-AACv2) аудио кодек. Обратите внимание, что сквозные live Events могут поддерживать разрешения до 4K со частотой 60 кадров в секунду. Для получения дополнительной [Live Event types comparison](live-event-types-comparison.md)информации см.

Разрешения и битраты, содержащиеся в выходе из живого кодера, определяются предустановленным. При использовании **стандартного** живого кодера (LiveEventEncodingType.Standard), то предустановка *Default720p* определяет набор из шести пар скорости разрешения/бита, переходя от 720p на 3,5 Мбит/с до 192p на 200 кбит/с. В противном случае, если с помощью **Premium1080p** жить кодер (LiveEventEncodingType.Premium1080p), то *Preset Default1080p* определяет набор из шести разрешений / битовых пар ставки, переходя от 1080p на 3,5 Мбит/с до 180p на 200 кбит/с. Дополнительные сведения см. в разделе о [системных предустановках](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Если вам нужно настроить предустановленное кодирование живого кодирования, откройте билет поддержки через портал Azure. Укажите нужную таблицу разрешения и битратов. Убедитесь, что есть только один слой на 720p (если запрашивать предустановленный для стандартного живого кодера) или на 1080p (если запрашивать предустановленный для Premium1080p живой кодер), и 6 слоев в лучшем случае.

## <a name="creating-live-events"></a>Создание живых событий

### <a name="options"></a>Параметры

При создании события потоковой трансляции можно указать следующие параметры.

* Протокол потоковой передачи для событий потоковой трансляции (сейчас поддерживаются протоколы RTMP и Smooth Streaming).<br/>Изменить протокол во время работы события потоковой трансляции или связанных с ним выходных данных потоковой трансляции нельзя. Если вам требуются различные протоколы, создайте отдельное событие Live event для каждого потокового протокола.  
* Для создаваемого события можно настроить автоматический запуск. <br/>Если для автозапуска задано значение true, событие прямой трансляции будет запущено после создания. Плата начисляется сразу же после запуска трансляции. Чтобы остановить начисление оплаты, нужно явно вызвать функцию Stop (Остановить) для ресурса события потоковой трансляции. Кроме того, вы можете начать событие, когда вы будете готовы начать потоковую передачу.

    Дополнительные сведения см. в статье [Состояния компонента LiveEvent и выставление счетов за его использование](live-event-states-billing.md).

* Ограничения по IP-адресам для приема и предварительного просмотра. Вы можете определить IP-адреса, с которых событие потоковой трансляции будет принимать видео. Можно указать отдельный допустимый IP-адрес (например, 10.0.0.1), или диапазон IP-адресов, используя IP-адрес и маску подсети CIDR (например, 10.0.0.1/22), или IP-адрес и маску подсети с точками (например, 10.0.0.1(255.255.252.0)).<br/>Если IP-адреса не указаны и правила не заданы, ни один IP-адрес не разрешен. Чтобы разрешить все IP-адреса, создайте правило и задайте адрес 0.0.0.0/0.<br/>IP-адреса должны быть в одном из следующих форматов: ipV4 адрес с четырьмя номерами или диапазон адресов CIDR.

    Если вы хотите включить определенные IP-адреса на собственных брандмауэрах или хотите ограничить входные данные на IP-адресаAzре, загрузите файл JSON с [IP-адресов Azure Datacenter.](https://www.microsoft.com/download/details.aspx?id=41653) Для получения подробной информации об этом файле выберите раздел **Подробная информация** на странице.
    
* При создании события, вы можете включить Live Транскрипции. <br/> По умолчанию, живая транскрипция отключена. Вы не можете изменить это свойство во время выполнения Live Event или связанных с ним живых выходов. 

### <a name="naming-rules"></a>Правила именования

* Макс живое имя события 32 символов.
* Имя должно следовать этому [шаблону regex:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

Также смотрите [конвенции именования потоковых конечных точек.](streaming-endpoint-concept.md#naming-convention)

> [!TIP]
> Чтобы гарантировать уникальность вашего живого названия события, вы можете создать GUID, а затем удалить все дефисы и фигурные скобки (если таковые были). Строка будет уникальной во всех живых событиях, и ее длина гарантированно составит 32.

## <a name="live-event-ingest-urls"></a>URL-адреса приема событий потоковой трансляции

После создания Live Event вы сможете получить URL-адреса, которые вы предоставите живому ан-кондеру. Он использует эти адреса для передачи потоковой трансляции. Для получения дополнительной информации, см [Рекомендуемые на месте жить кодеров](recommended-on-premises-live-encoders.md).

Можно использовать запоминающиеся и незапоминающиеся URL-адреса.

> [!NOTE] 
> Чтобы URL-адрес был прогнозируемым, необходимо использовать режим запоминающихся адресов.

* Незапоминающийся URL-адрес

    URL-адрес Non-vanity — это режим по умолчанию в Media Services v3. Таким образом, вы можете быстро получить событие потоковой трансляции, но URL-адрес приема будет известен только после запуска события. URL-адрес изменится, если вы остановите или запустите событие потоковой трансляции. <br/>Non-Vanity полезен в сценариях, когда конечный пользователь хочет передавать потоковую передачу с помощью приложения, где приложение хочет получить живое событие как можно скорее, и наличие динамического URL-адреса не является проблемой.

    Если клиенту-приложению не нужно предварительно генерировать URL-адрес до создания Live Event, позвольте Media Services автоматически вызвать токен access Token для события в реальном времени.

* Запоминающийся URL-адрес

    Режим тщеславия предпочитают крупные вещательные компании, которые используют аппаратные кодора и не хотят перенастраивать свои кодеры при запуске Live Event. Они хотят прогностический URL-адрес, который не изменяется с течением времени.
    
    > [!NOTE]
    > На портале Azure URL тщеславия называется *«Постоянный URL ввода».*

    Указать этот режим в API, `vanityUrl` `true` установленном на `false`момент создания (по умолчанию является). Вы также должны пройти свой собственный токен доступа ()`LiveEventInput.accessToken`во время создания. Вы указываете значение маркера, чтобы избежать случайного маркера в URL. Токен доступа должен быть действительной строкой GUID (с дефисом или без нее). После установки режима он не может быть обновлен.

    Токен доступа должен быть уникальным в вашем центре обработки данных. Если вашему приложению необходимо использовать URL-адрес тщеславия, рекомендуется всегда создавать новый экземпляр GUID для маркера доступа (вместо повторного использования любого существующего GUID).

    Используйте следующие AA, чтобы включить URL-адрес Тщеславия и `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`установить токен доступа к действительному GUID (например, ).  

    |Язык|Включить URL тщеславия|Задание маркера доступа.|
    |---|---|---|
    |REST|[свойства.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--тщеславие-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--доступ-токен](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Правила именования URL-адресов динамического приема

* *Случайная* строка ниже представляет собой 128-разрядное шестнадцатеричное число (состоящее из 32 знаков: 0–9 и a–f).
* *токен доступа:* действительная строка GUID, установленная при использовании режима тщеславия. Например, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *имя потока*: Указывает имя потока для определенного соединения. Значение имени потока обычно добавляется используемым кодером. Вы можете настроить живой кодер, чтобы использовать любое имя для описания соединения, например: "video1_audio1", "video2_audio1", "поток".

#### <a name="non-vanity-url"></a>Незапоминающийся URL-адрес

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Запоминающийся URL-адрес

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL-адрес для предварительного просмотра событий потоковой трансляции

Как только Live Event начинает получать канал вклада, вы можете использовать его конечную точку предварительного просмотра для предварительного просмотра и проверки того, что вы получаете живой эфир перед публикацией. После проверки того, что поток предварительного просмотра является хорошим, вы можете использовать Live Event, чтобы сделать живой поток доступным для доставки через одну или несколько (предварительно созданных) потоковых точек. Для достижения этой цели создайте новый [выход live](https://docs.microsoft.com/rest/api/media/liveoutputs) на Live Event.

> [!IMPORTANT]
> Прежде чем продолжить, убедитесь, что видео правильно передается на URL-адрес предварительного просмотра.

## <a name="live-event-long-running-operations"></a>Долгосрочные операции Live Event

Для получения подробной информации [см.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Динамичесие выходные данные

После того, как поток, впадающий в Live Event, вы можете начать потоковое событие, создав [актив,](https://docs.microsoft.com/rest/api/media/assets) [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs)и [Streaming Locator.](https://docs.microsoft.com/rest/api/media/streaminglocators) Выходные данные потоковой трансляции запустят архивирование потока и предложат его зрителям через [конечную точку потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Для получения [подробной](live-event-cloud-dvr.md)информации о Live Outputs см.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

Смотрите статью [О часто задаваемые вопросы.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Задавайте вопросы и получайте обновления

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="next-steps"></a>Дальнейшие действия

[Руководство по потоковой трансляции](stream-live-tutorial-with-api.md)
