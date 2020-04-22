---
title: Полная настройка мультиплеера Azure
description: Узнайте, как настроить медиаплеер Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727246"
---
# <a name="azure-media-player-full-setup"></a>Полная настройка мультиплеера Azure #

Проигрыватель мультимедиа Azure можно легко настроить. Требуется всего несколько минут, чтобы получить базовое воспроизведение медиаконтента прямо из вашей учетной записи Media Services Azure. [Образцы](https://github.com/Azure-Samples/azure-media-player-samples) также предоставляются в каталоге образцов релиза.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Шаг 1: Включите файлы JavaScript и CSS в главу страницы ##

С помощью Azure Media Player вы можете получить доступ к скриптам из разводной версии CDN. Это часто рекомендуется сейчас поставить JavaScript `<body>` перед конечным тегом тела вместо `<head>`, но Azure Media Player включает в себя 'HTML5 Shiv', который должен быть в голове для старых версий IE уважать видео тег и действительный элемент.

> [!NOTE]
> Если вы уже используете HTML5 shiv, как [Modernizr,](http://modernizr.com/) вы можете включить Медиаплеер Azure JavaScript в любом месте. Однако убедитесь, что ваша версия Modernizr включает в себя shiv для видео.

### <a name="cdn-version"></a>CDN Версия ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> Вы **не** должны `latest` использовать версию в производстве, так как это может быть изменено по требованию. Замените `latest` версию медиаплеера Azure. Например, замените `latest` на `2.1.1`. Версии Мультиплейплеера Azure можно запрашивать [отсюда.](azure-media-player-changelog.md)

> [!NOTE]
> После `1.2.0` выпуска, это больше не требуется, чтобы включить местоположение в запасной техники (он будет автоматически забрать местоположение от относительного пути azuremediaplayer.min.js файл). Вы можете изменить местоположение резервных техников, добавив следующий сценарий в `<head>` следующих сценариях.

> [!NOTE]
> В связи с характером плагинов Flash и Silverlight, файлы swf и xap должны размещаться на домене без какой-либо конфиденциальной информации или данных - это автоматически заботятся о вас с Azure CDN размещается версия.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Шаг 2: Добавить HTML5 видео тег на вашу страницу ##

С помощью Azure Media Player вы можете использовать видеотег HTML5 для встраиваемого видео. Затем Azure Media Player прочитает тег и заставит его работать во всех браузерах, а не только в тех, которые поддерживают HTML5-видео. Помимо базовой разметки, Azure Media Player требуется несколько дополнительных частей.

1. Атрибут `<data-setup>` в `<video>` мультиплеере Лазурного средства автоматически настраивает видео, когда страница будет готова, и читает любое (в формате JSON) из атрибута.
1. Атрибут: `id` Должен быть использован и уникален для каждого видео на той же странице.
1. Атрибут `class` содержит два класса:
    - `azuremediaplayer`применяет стили, необходимые для функциональности пользовательского завиотки мультиплеера Azure Media Player
    - `amp-default-skin`применяет кожу по умолчанию к элементам управления HTML5
1. Включает `<source>` в себя два необходимых атрибута
    - `src`атрибут может включать файл *.ism/manifest* от Медиа-службы Azure, Azure Media Player автоматически добавляет URL-адреса для DASH, SMOOTH и HLS для игрока
    - `type`атрибутом является необходимый тип потока MIME. Тип MIME, связанный с *".ism/manifest"* , является *"приложение/vnd.ms-sstr'xml"*
1. *Дополнительный* `<data-setup>` атрибут `<source>` в мультиплеере Azure, если есть какие-либо уникальные политики доставки для потока из медиаслужб Azure, включая, но не ограничиваясь, тип шифрования (AES или PlayReady, Widevine или FairPlay) и токен.

Включите/исключите атрибуты, настройки, источники и треки точно так же, как для HTML5-видео.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

По умолчанию большая кнопка воспроизведения находится в верхнем левом углу, поэтому она не скрывает интересные части плаката. Если вы предпочитаете центрировать большую кнопку воспроизведения, вы можете добавить дополнительный `amp-big-play-centered` `class` к элементу. `<video>`

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Альтернативная настройка для динамически загруженного HTML ###

Если веб-страница или приложение загружает тег видео динамически (ajax, appendChild и т.д.), так что он может не существовать при загрузке страницы, вы хотите вручную настроить плеер вместо того, чтобы полагаться на атрибут настройки данных. Для этого сначала удалите атрибут настройки данных из тега, чтобы не было путаницы вокруг, когда игрок инициализирован. Затем запустите следующий JavaScript через некоторое время после загрузки Медиаплеера Azure, и после того, как тег видео был загружен в DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Первым аргументом `amp` в функции является идентификатор вашего видеотега. Замените его своим.

Второй аргумент — объект опций. Это позволяет установить дополнительные параметры, как вы можете с атрибутом настройки данных.

Третий аргумент — «готовый» обратный вызов. Как только Медиаплеер Azure инициализировался, он вызовет эту функцию. В готовом обратном вызове "этот" объект относится к экземпляру игрока.

Вместо того, чтобы использовать идентификатор элемента, можно также передать ссылку на сам элемент.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Следующие шаги ##

- [Быстрый запуск медиаплеера Azure](azure-media-player-quickstart.md)