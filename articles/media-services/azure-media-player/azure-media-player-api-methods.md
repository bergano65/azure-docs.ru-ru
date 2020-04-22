---
title: Методы API Медиа-плеера Azure
description: API-изготовитель Мультимедиа Позволяет взаимодействовать с видео через JavaScript, независимо от того, воспроизводить ли браузер видео через HTML5-видео, Flash, Silverlight или любые другие поддерживаемые технологии воспроизведения.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727272"
---
# <a name="api"></a>API #

API-изготовитель Мультимедиа Позволяет взаимодействовать с видео через JavaScript, независимо от того, воспроизводить ли браузер видео через HTML5-видео, Flash, Silverlight или любые другие поддерживаемые технологии воспроизведения.

## <a name="referencing-the-player"></a>Ссылка на игрока ##

Для использования функций API необходим доступ к объекту игрока. К счастью, это легко получить. Вам просто нужно убедиться, что ваш видеотег имеет удостоверение личности. Пример встраиваемого `vid1`кода имеет идентификатор . Если у вас есть несколько видео на одной странице, убедитесь, что каждый видеотег имеет уникальный идентификатор.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Если игрок еще не был инициализирован с помощью атрибута настройки данных или другого метода, это также инициализирует игрока.

## <a name="wait-until-the-player-is-ready"></a>Подождите, пока игрок будет готов ##

Время настройки мультиплеера Azure media Player будет варьироваться в зависимости от используемой технологии воспроизведения. HTML5 часто будет гораздо быстрее загружаться, чем Flash или Silverlight. По этой причине функция игрока «готова» должна использоваться для запуска любого кода, требующего API игрока.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OR

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Методы API ##

Теперь, когда у вас есть доступ к готовому проигрыватель, вы можете управлять видео, получать значения или реагировать на видеособытия. Названия функций API Мультимедиа Плеер пытаются следовать [API мультимедиа HTML5.](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html) Основное отличие заключается в том, что функции getter/setter используются для свойств видео.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Регистрация на мероприятия ##
События должны быть зарегистрированы сразу после инициализации игрока в первый раз, чтобы убедиться, что все события должным образом доведены до сведения приложения, и должны быть сделаны за пределами готового события.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Следующие шаги ##

<!---Some context for the following links goes here--->
- [Быстрый запуск медиаплеера Azure](azure-media-player-quickstart.md)