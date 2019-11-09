---
title: Внедрение графических мини-приложений индексатора видео в приложения
titleSuffix: Azure Media Services
description: Сведения о внедрении мини-приложений индексатора видео в приложение.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 99d6647ab5e7fa8f35cef883dd00ae9fea866370
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839118"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Внедрение графических мини-приложений индексатора видео в приложения

В этой статье показано, как внедрять графические элементы индексатора видео в приложения. Индексатор видео поддерживает внедрение трех типов мини-приложений в приложение *: «* автоанализ», « *проигрыватель*» и « *Редактор*». 

Начиная с версии 2, базовый URL-адрес Widget включает в себя регион указанной учетной записи. Например, создается учетная запись в западной части США: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Типы мини-приложений

### <a name="cognitive-insights-widget"></a>Мини-приложение Cognitive Insights

Мини-приложение для анализа данных содержит все визуальные сведения, извлеченные из процесса индексирования видео. Мини-приложение для анализа сведений поддерживает следующие необязательные параметры URL.

|Имя|Определение|Description (Описание)|
|---|---|---|
|`widgets`|Строки, разделенные запятыми.|Позволяет управлять аналитическими сведениями, которые необходимо визуализировать. <br/> Пример. `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` отображает только сведения о ПОЛЬЗОВАТЕЛЯх и торговых марках.<br/>Доступные значения: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Обратите внимание, что параметр URL-адреса `widgets` не поддерживается в версии 2.<br/>|
|`locale`|Короткий код языка|Управляет языком Insights. По умолчанию используется значение `en`. <br/> Пример: `locale=de`.|
|`tab`|Выбранная по умолчанию вкладка|Управляет вкладкой **Insights** , которая отображается по умолчанию. <br/> Пример. `tab=timeline` отображает аналитические сведения с выбранной вкладкой **временной шкалы** .|

### <a name="player-widget"></a>Мини-приложение Player

Вы можете использовать мини-приложение проигрывателя для потоковой передачи видео с использованием адаптивной скорости. Мини-приложение проигрывателя поддерживает следующие необязательные параметры URL.

|Имя|Определение|Description (Описание)|
|---|---|---|
|`t`|Секунд с начала|Запускает воспроизведение проигрывателя с указанной временной точки.<br/> Пример: `t=60`.|
|`captions`|Код языка|Извлекает заголовок на указанном языке во время загрузки мини-приложения, чтобы быть доступным в меню **субтитров** .<br/> Пример: `captions=en-US`.|
|`showCaptions`|Логическое значение|Позволяет проигрывателю загружаться с уже включенными субтитрами.<br/> Пример: `showCaptions=true`.|
|`type`||Активирует обложку аудио Player (часть видео удаляется).<br/> Пример: `type=audio`.|
|`autoplay`|Логическое значение|Указывает, должен ли проигрыватель начать воспроизведение видео при загрузке. По умолчанию используется значение `true`.<br/> Пример: `autoplay=false`.|
|`language`|Код языка|Управляет языком проигрывателя. По умолчанию используется значение `en-US`.<br/>Пример: `language=de-DE`.|

### <a name="editor-widget"></a>Мини-приложение редактора

Вы можете использовать мини-приложение редактора для создания новых проектов и управления аналитическими данными видео. Мини-приложение редактора поддерживает следующие необязательные параметры URL.

|Имя|Определение|Description (Описание)|
|---|---|---|
|`accessToken`<sup>*</sup>|string|Предоставляет доступ к видео, которые используются только в учетной записи, используемой для внедрения мини-приложения.<br> Мини-приложению редактора требуется параметр `accessToken`.|
|`language`|Код языка|Управляет языком проигрывателя. По умолчанию используется значение `en-US`.<br/>Пример: `language=de-DE`.|
|`locale`|Короткий код языка|Управляет языком Insights. По умолчанию используется значение `en`.<br/>Пример: `language=de`.|

<sup>*</sup> Владелец должен предоставить `accessToken` с осторожностью.

## <a name="embedding-public-content"></a>Внедрение общедоступного содержимого

1. Войдите на веб-сайт [индексатора видео](https://www.videoindexer.ai/) .
2. Выберите видео, с которым хотите работать.
3. Нажмите кнопку **внедрить** , которая отображается под видео.

    ![Мини-приложение](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    После нажатия кнопки **внедрить** можно выбрать мини-приложение, которое вы хотите внедрить в свое приложения. 
4. Выберите нужный тип мини-приложения («**автоаналитические**сведения», « **проигрыватель**» или « **Редактор**»).
 
5. Скопируйте код внедрения и добавьте его в приложение. 

    ![Мини-приложение](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Если у вас возникли проблемы с общими URL-адресами видео, добавьте параметр `location` в ссылку. Для параметра должны быть заданы [регионы Azure, в которых существует индексатор видео](regions.md). Например, `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Внедрение личного содержимого

Чтобы внедрить частное видео, необходимо передать маркер доступа в атрибут **src** IFRAME:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Чтобы получить содержимое мини-приложения для анализа данных, выполните одно из следующих действий.<br/>
- API [мини-приложения Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- [Маркер доступа для получения видео](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?). Добавьте его в качестве параметра запроса к URL-адресу. Укажите этот URL-адрес в качестве значения **src** для IFRAME, как показано выше.

Чтобы предоставить возможности изменения аналитических сведений во внедренном мини-приложении, необходимо передать маркер доступа, включающий разрешения на редактирование. Используйте [мини-приложение Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) или [Получите маркер доступа к видео](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) с помощью `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Взаимодействие с мини-приложениями

Мини-приложение «информированная Аналитика» может взаимодействовать с видео в приложении. В этом разделе показано, как реализовать это взаимодействие.

![Мини-приложение](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Обмен данными независимо от источника

Чтобы получить графические элементы индексатора видео для взаимодействия с другими компонентами, служба индексатора видео:

- Использует перекрестное **сообщение**из метода HTML5 для обмена данными с разными источниками. 
- проверяет сообщения, поступающие из источника VideoIndexer.ai и в него. 

Если вы реализуете собственный код проигрывателя и интегрируетесь с мини-приложениями для «автоанализа», вы обязаны проверить происхождение сообщения, поступающих от VideoIndexer.ai.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Внедрение мини-приложений в приложение или блог (рекомендуется) 

В этом разделе показано, как обеспечить взаимодействие между двумя мини-приложениями индексатора видео, чтобы при выборе пользователем элемента управления "аналитика" для приложения игрок переходит к соответствующему моменту.

1. Скопируйте код внедрения мини-приложения проигрывателя.
2. Скопируйте код внедрения для «автоанализа».
3. Добавьте [файл медиатора](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) для управления связью между двумя мини-приложениями:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Теперь, когда пользователь выбирает элемент управления Insights в приложении, игрок переходит к соответствующему моменту.

Дополнительные сведения см. в статье [индексатор видео — внедрение как мини-приложений демонстрации](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Внедрение мини-приложения Cognitive Insights и использование Проигрывателя мультимедиа Azure для воспроизведения содержимого

В этом разделе показано, как обеспечить взаимодействие мини-приложения "автоаналитическая аналитика" и экземпляра Проигрыватель мультимедиа Azure с помощью [подключаемого модуля amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Добавьте подключаемый модуль индексатора видео для проигрывателя AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Создайте экземпляр Проигрыватель мультимедиа Azure с помощью подключаемого модуля индексатора видео.

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
            syncLanguage: true
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Скопируйте код внедрения для «автоанализа».

Теперь вы можете взаимодействовать с Проигрыватель мультимедиа Azure.

Дополнительные сведения см. на странице [демонстрации проигрыватель мультимедиа Azure + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Внедрение мини-приложения "автоаналитическая аналитика" для индексатора видео и использование другого видеопроигрывателя

При использовании видеопроигрывателя, отличного от Проигрыватель мультимедиа Azure, необходимо вручную управлять видеопроигрывателем, чтобы обеспечить взаимодействие. 

1. Вставьте свой видеопроигрыватель.

    Например, стандартный проигрыватель HTML5:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Внедрите мини-приложение Cognitive Insights.
3. Реализуйте обмен данными для проигрывателя, настроив ожидание передачи данных о событии message. Например:

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

Дополнительные сведения см. на странице [демонстрации проигрыватель мультимедиа Azure + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Добавление субтитров

При внедрении индексатора видео с помощью собственного [проигрыватель мультимедиа Azure](https://aka.ms/azuremediaplayer)можно использовать метод **жетвттурл** для получения скрытых субтитров (субтитров). Кроме того, можно вызвать метод JavaScript из подключаемого модуля индексатора управления видео AMP **жетсубтитлесурл** (как показано выше). 

## <a name="customizing-embeddable-widgets"></a>Настройка внедряемых мини-приложений

### <a name="cognitive-insights-widget"></a>Мини-приложение Cognitive Insights

Вы можете выбрать нужные типы аналитики. Для этого укажите их в качестве значения для следующего параметра URL-адреса, который добавляется в полученный код внедрения (из API или из веб-приложения): `&widgets=<list of wanted widgets>`.

Возможные значения: **люди**, **Ключевые слова**, **тональности**, **транскрипция**и **Поиск**.

Например, если вы хотите внедрить мини-приложение, которое содержит только людей и поисковые данные, URL внедрения IFRAME будет выглядеть следующим образом:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Заголовок окна iframe можно настроить, указав `&title=<YourTitle>` в URL-адресе для iframe. (Настраивается > значение заголовка HTML \<).
    
Например, если для окна iframe нужно указать заголовок MyInsights, URL-адрес будет выглядеть так:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Обратите внимание на то, что этот параметр применим, только когда аналитические сведения нужно открыть в новом окне.

### <a name="player-widget"></a>Мини-приложение Player

При внедрении проигрывателя Индексатора видео можно выбрать размер проигрывателя, указав размер для элемента iframe.

Например:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

По умолчанию проигрыватель индексаторов видео автоматически создает скрытые субтитры, основанные на транскрипции видео. Запись извлекается из видео с использованием исходного языка, выбранного при загрузке видео.

Если вы хотите внедрить с другим языком, можно добавить `&captions=< Language | "all" | "false" >` URL-адресу внедрения проигрывателя. Если нужно, чтобы субтитры во всех заголовках всех языков были доступными, используйте значение `all`. Чтобы субтитры отображались по умолчанию, передайте `&showCaptions=true`.

URL-адрес внедрения будет выглядеть так: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Если вы хотите отключить субтитры, можно передать значение параметра `captions` как `false`.

#### <a name="autoplay"></a>Автозапуска
По умолчанию проигрыватель начнет воспроизведение видео. Вы можете отказаться от передачи `&autoplay=false` к предыдущему URL-адресу внедрения.

## <a name="next-steps"></a>Дальнейшие действия

Сведения о просмотре и редактировании индексатора видео см. в статье [Просмотр и изменение индексаторов видео](video-indexer-view-edit.md).

Кроме того, ознакомьтесь с [CodePenом индексатора видео](https://codepen.io/videoindexer/pen/eGxebZ).
