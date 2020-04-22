---
title: Написание плагинов для медиаплеера Azure
description: Узнайте, как написать плагин с помощью медиаплеера Azure с помощью JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727116"
---
# <a name="writing-plugins-for-azure-media-player"></a>Написание плагинов для медиаплеера Azure #

Плагин JavaScript написан для расширения или повышения игрока. Вы можете написать плагины, которые меняют внешний вид, его функциональность Azure Media Player или даже имеют его интерфейс с другими службами. Вы можете сделать это в двух простых шагах:

## <a name="step-1"></a>Шаг 1 ##

Напишите JavaScript в такой функции:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Вы можете написать свой код `<script>` непосредственно на странице HTML в тегах или во внешнем файле JavaScript. Если вы делаете последнее, не забудьте включить `<head>` файл JavaScript в вашей странице HTML *после* сценария AMP.

Пример.

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Шаг 2 ##
Инициализировать плагин с JavaScript одним из двух способов:

Метод 1.

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Метод 2.

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Параметры плагина не требуются, в том числе они просто позволяет разработчикам, использующим плагин, настроить его поведение без необходимости менять исходный код.

Для вдохновения и больше примеров по созданию плагина взгляните на нашу [галерею](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Код плагина динамически изменяет элементы в DOM в течение всего срока службы игрока зрителя, он никогда не вносит постоянные изменения в исходный код игрока. Это где понимание инструментов разработчика вашего браузера пригодится. Например, если вы хотите изменить внешний вид элемента в плеере, вы можете найти его HTML элемент по названию класса, а затем добавить или изменить атрибуты оттуда. Вот большой ресурс по [изменению HTML атрибутов.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Интегрированные плагины ###

 Есть в настоящее время два плагина запеченные в AMP: [время-наконечник](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) и [hotkeys](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Эти плагины были первоначально разработаны, чтобы быть модульными плагинами для игрока, но теперь включены в исходный код игрока.

### <a name="plugin-gallery"></a>Плагин Галерея ###

[Плагин галерея](http//:aka.ms/ampplugins) имеет несколько плагинов, что сообщество уже способствовало для функций, таких как маркеры временной линии, зум, аналитика и многое другое. Страница предоставляет доступ к плагинам и инструкции о том, как настроить его, а также демо, который показывает плагин в действии. Если вы создаете прохладный плагин, который вы думаете, должны быть включены в нашу галерею, не стесняйтесь представить его, чтобы мы могли проверить его.

## <a name="next-steps"></a>Следующие шаги ##

- [Быстрый запуск медиаплеера Azure](azure-media-player-quickstart.md)