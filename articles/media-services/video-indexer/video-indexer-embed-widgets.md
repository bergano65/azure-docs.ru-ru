---
title: Встраивайте виджеты видеоиндекса в ваши приложения
titleSuffix: Azure Media Services
description: Узнайте, как вставлять виджеты Video Indexer в ваши приложения.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: e475c1bc1878c6b5a0efbbe41f2a3a0fe86bcff2
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389381"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Встраивайте виджеты видеоиндекса в ваши приложения

В этой статье показано, как можно вставлять виджеты Video Indexer в свои приложения. Video Indexer поддерживает встраивание трех типов виджетов в приложения: *Cognitive Insights,* *Player*и *Editor.*

Начиная с версии 2, URL-адрес базы виджетов включает область указанной учетной записи. Например, создается учетная запись в западной части США: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Типы мини-приложений

### <a name="cognitive-insights-widget"></a>Мини-приложение Cognitive Insights

Мини-приложение Cognitive Insights содержит все визуальные аналитические сведения, извлеченные при индексировании видео. Виджет Cognitive Insights поддерживает следующие дополнительные параметры URL:

|name|Определение|Описание|
|---|---|---|
|`widgets` | Строки, разделенные запятыми. | Позволяет управлять информацией, которую вы хотите сделать.<br/>Пример: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` отображает только пользователи и ключевые слова uI идеи.<br/>Доступные варианты: люди, анимированныеПерсонажи, ключевые слова, этикетки, настроения, эмоции, темы, ключевые кадры, транскрипт, ocr, динамики, сцены и namedEntities.|
|`controls`|Строки, разделенные запятыми.|Позволяет управлять элементами управления, которые вы хотите сделать.<br/>Пример: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` отображает только опцию поиска и кнопку загрузки.<br/>Доступные варианты: поиск, загрузка, пресеты, язык.|
|`language`|Код короткого языка (имя языка)|Контролирует язык проницательности.<br/>Пример: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>или `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Короткий языковой код | Контролирует язык uI. Значение по умолчанию — `en`. <br/>Например, `locale=de`.|
|`tab` | Выбранная вкладка по умолчанию | Контролирует вкладку **Insights,** отображаемую по умолчанию. <br/>Пример: `tab=timeline` отображаются идеи с выбранной вкладкой **Хронология.**|

### <a name="player-widget"></a>Мини-приложение Player

Вы можете использовать виджет Player для потоковой передачи видео с помощью адаптивного бита. Виджет Player поддерживает следующие дополнительные параметры URL.

|name|Определение|Описание|
|---|---|---|
|`t` | Секунды с самого начала | Заставляет игрока начать играть с указанного момента времени.<br/> Например, `t=60`. |
|`captions` | Языковой код | Получает подпись на указанном языке во время загрузки виджета, которая будет доступна в меню **Подписей.**<br/> Например, `captions=en-US`. |
|`showCaptions` | Значение булеана | Позволяет проигрывателю загружаться с уже включенными субтитрами.<br/> Например, `showCaptions=true`. |
|`type`| | Активирует кожу аудиоплеера (видео часть удаляется).<br/> Например, `type=audio`. |
|`autoplay` | Значение булеана | Указывает, должен ли игрок начать воспроизведение видео при загрузке. Значение по умолчанию — `true`.<br/> Например, `autoplay=false`. |
|`language`/`locale` | Языковой код | Контролирует язык игрока. Значение по умолчанию — `en-US`.<br/>Например, `language=de-DE`.|

### <a name="editor-widget"></a>Виджет редактора

Вы можете использовать виджет редактора для создания новых проектов и управления информацией о видео. Виджет редакторподдерживает следующие дополнительные параметры URL.

|name|Определение|Описание|
|---|---|---|
|`accessToken`<sup>*</sup> | Строка | Предоставляет доступ к видео, которые находятся только в учетной записи, которая используется для встраиваемого виджета.<br> Виджет редактора `accessToken` требует параметра. |
|`language` | Языковой код | Контролирует язык игрока. Значение по умолчанию — `en-US`.<br/>Например, `language=de-DE`. |
|`locale` | Короткий языковой код | Контролирует язык проницательности. Значение по умолчанию — `en`.<br/>Например, `language=de`. |

<sup>*</sup>Владелец должен `accessToken` проявлять осторожность.

## <a name="embedding-public-content"></a>Внедрение общедоступного содержимого

1. Войти на веб-сайт [Video Indexer.](https://www.videoindexer.ai/)
2. Выберите видео, с которым вы хотите работать.
3. Выберите кнопку **Встраиваем,** которая появляется под видео.

    ![Встраиваемые кнопки в видеоиндексе](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    После выбора кнопки **«Встраиваемый»** можно выбрать виджет, который вы хотите вставить в приложение.
4. Выберите тип виджета, который вы хотите (**Когнитивные Исследования**, **Игрок,** или **редактор**).
 
5. Скопируйте встраиваемый код, а затем добавьте его в приложение.

    ![Встраиваемый код для приложения-Видео Индексер](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Если у вас возникли проблемы `location` с обменом URL-адресами видео, добавьте параметр в ссылку. Параметр должен быть установлен в [регионах Azure, в которых существует Индекс-индекс.](regions.md) Например: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Внедрение личного содержимого

Чтобы вставить приватное видео, необходимо передать `src` токен доступа в атрибуте iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Чтобы получить содержимое виджетов Cognitive Insights, используйте один из следующих методов:

- [Get Insights Виджет](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API.<br/>
- [Получить видео-доступ токен](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Добавьте его в качестве параметра запроса в URL. Укажите этот `src` URL как значение для iframe, как показано ранее.

Чтобы обеспечить возможности редактирования в встроенном виджете, необходимо передать токен доступа, включающий разрешения на редактирование. Используйте [Get Insights Виджет](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) или [получить токен доступа к видео](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) с `&allowEdit=true`.

## <a name="widgets-interaction"></a>Взаимодействие с мини-приложениями

Виджет Cognitive Insights может взаимодействовать с видео в приложении. В этом разделе показано, как реализовать это взаимодействие.

![Когнитивные Исследования виджет Видео Индексер](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Обмен данными независимо от источника

Чтобы получить видео индексер виджеты для общения с другими компонентами, видео индексатор службы:

- Использует метод `postMessage`связи между корнями HTML5.
- проверяет сообщения, поступающие из источника VideoIndexer.ai и в него.

Если вы реализуете свой собственный код игрока и интегрируетесь с виджетами Cognitive Insights, вы обязаны проверить происхождение сообщения, которое исходит от VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Встраивайте виджеты в приложение или блог (рекомендуется)

В этом разделе показано, как достичь взаимодействия между двумя виджетами Video Indexer, чтобы, когда пользователь выбирал элемент управления пониманием вашего приложения, игрок перепрыгивай в соответствующий момент.

1. Скопируйте код внедрения для мини-приложения Player.
2. Скопируйте код внедрения для Cognitive Insights.
3. Добавьте [файл медиатора](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js), чтобы реализовать взаимодействие между двумя мини-приложениями:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Теперь, когда пользователь выбирает элемент управления пониманием в вашем приложении, игрок переходит к соответствующему моменту.

Для получения дополнительной информации, [см. Видео Indexer - Встраиваемые оба Widgets демо](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Внедрение мини-приложения Cognitive Insights и использование Проигрывателя мультимедиа Azure для воспроизведения содержимого

В этом разделе показано, как достичь взаимодействия между виджетом Cognitive Insights и экземпляром Мультиплейра Azure с помощью [плагина AMP.](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)

1. Добавьте плагин Video Indexer для игрока AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Мгновенное медиаплеер Azure с помощью плагина Video Indexer.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Скопируйте код внедрения для Cognitive Insights.

Теперь вы можете общаться с медиаплеером Azure.

Для получения дополнительной [информации](https://codepen.io/videoindexer/pen/rYONrO)см.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Встраивайте виджет Video Indexer Cognitive Insights и используйте другой видеоплеер

Если вы используете видеоплеер, кроме Мультиплейра Azure, вы должны вручную манипулировать видеоплеером для достижения связи.

1. Вставьте свой видеопроигрыватель.

    Например, стандартный HTML5-плеер:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Внедрите мини-приложение Cognitive Insights.
3. Реализуйте обмен данными для проигрывателя, настроив ожидание передачи данных о событии message. Пример:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Для получения дополнительной [информации](https://codepen.io/videoindexer/pen/YEyPLd)см.

## <a name="adding-subtitles"></a>Добавление субтитров

Если вы встраите данные Video Indexer с помощью `GetVttUrl` собственного [медиаплеера Azure,](https://aka.ms/azuremediaplayer)вы можете использовать метод для получения закрытых субтитров (субтитров). Вы также можете вызвать метод JavaScript из плагина Video Indexer AMP `getSubtitlesUrl` (как показано ранее).

## <a name="customizing-embeddable-widgets"></a>Настройка внедряемых мини-приложений

### <a name="cognitive-insights-widget"></a>Мини-приложение Cognitive Insights

Вы можете выбрать типы идей, которые вы хотите. Для этого укажите их в качестве значения для следующего параметра URL, который добавляется к встраиваемому коду, который вы получаете (из API или из веб-приложения): `&widgets=<list of wanted widgets>`

Возможные `people`значения: , `animatedCharacters` `keywords`, `labels` `sentiments`, `emotions` `topics`, `keyframes` `transcript`, `ocr` `speakers`, `scenes`, `namedEntities`, , , и .

Например, если вы хотите вставить виджет, содержащий только сведения о людях и ключевых словах, URL-адрес iframe будет выглядеть следующим образом:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Заголовок окна iframe можно настроить, указав `&title=<YourTitle>` в URL-адресе для iframe. (Он настраивает <title> значение HTML).
   
Например, если для окна iframe нужно указать заголовок MyInsights, URL-адрес будет выглядеть так:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Обратите внимание на то, что этот параметр применим, только когда аналитические сведения нужно открыть в новом окне.

### <a name="player-widget"></a>Мини-приложение Player

При внедрении проигрывателя Индексатора видео можно выбрать размер проигрывателя, указав размер для элемента iframe.

Пример:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

По умолчанию проигрыватель Video Indexer имеет автоматически генерируемые закрытые подписи, основанные на стенограмме видео. Стенограмма извлекается из видео с исходным языком, который был выбран при загрузке видео.

Если вы хотите вставить другой язык, вы можете добавить &подписи< языкового кода > в встраиваемый URL-адрес игрока. Если вы хотите, чтобы подписи отображались по умолчанию, вы можете пройти &showCaptions'true.

URL-адрес внедрения будет выглядеть так:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Автозапуск

По умолчанию игрок начнет воспроизведение видео. Вы можете отказаться `&autoplay=false` от переадресаний в предыдущий встраиваемый URL- адрес.

## <a name="code-samples"></a>Примеры кода

Смотрите репо [образцов кода,](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) содержащее образцы для Video Indexer API и виджетов:

| Файл или папка                       | Описание                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Загрузите видео-индексер видео в пользовательском медиаплеере Azure.                        |
| `azure-media-player-vi-insights`  | Влаживайте VI Insights с помощью пользовательского медиаплеера Azure.                             |
| `control-vi-embedded-player`      | Встраивайте VI Player и контролируйте его извне.                                    |
| `custom-index-location`           | Встраивайте VI Insights из пользовательского внешнего местоположения (может быть клиентом капли).     |
| `embed-both-insights`             | Основное использование VI Исследования как игрок и идеи.                            |
| `embed-insights-with-AMP`         | Встраивайте виджет VI Insights с помощью пользовательского медиаплеера Azure.                      |
| `customize-the-widgets`           | Встраивайте виджеты VI с индивидуальными опциями.                                     |
| `embed-both-widgets`              | Встраивайте VI Player и Insights и общайтесь между ними.                      |
| `url-generator`                   | Генерирует виджеты пользовательских встраиваемый URL на основе пользовательских параметров.             |
| `html5-player`                    | Встраивай VI Insights с помощью видеоплеера HTML5 по умолчанию.                           |

## <a name="next-steps"></a>Дальнейшие действия

Для получения информации о том, как просматривать и отсылать данные Video Indexer, [просмотрите и отодвинете информацию о видеоиндексе.](video-indexer-view-edit.md)

Кроме того, проверить [видео индексер CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
