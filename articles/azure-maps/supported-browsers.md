---
title: Веб-пакет SDK поддерживаемые браузеры - "карты Azure" | Документация Майкрософт
description: Дополнительные сведения о поддерживаемых браузерах для SDK веб-карт Azure
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577345"
---
# <a name="web-sdk-supported-browsers"></a>Браузеры, поддерживаемые веб-пакет SDK

SDK веб-карты Azure предоставляет вспомогательную функцию [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) для обнаружения, если браузер имеет минимальное приведенные WebGL компоненты для поддержки загрузки и Подготовка к просмотру элемента управления картой. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Классические приложения

SDK веб-карты Azure поддерживает следующие браузеры рабочего стола.

- Текущие и предыдущие версии Microsoft Edge 
- Текущие и предыдущие версии Chrome 
- Текущие и предыдущие версии Firefox 
- Текущие и предыдущие версии Safari (Mac OS X) 

См. также [предназначенных для предыдущих версий браузеров](#Target-Legacy-Browsers).

## <a name="mobile"></a>Мобильные приложения

SDK веб-карты Azure поддерживает следующие браузеры мобильных устройств.

-  Android
    * Текущая версия Chrome в Android 6.0 +
    * Chrome WebView Android 6.0 +
- iOS
    * Safari Mobile в текущей и предыдущей основной номер версии iOS
    * UIWebView и WKWebView на текущим и предыдущим основной номер версии iOS
    * Текущая версия Chrome для iOS

> [!TIP]
> Если вы внедряете карты в мобильных приложений при помощи элемента управления WebView, вы предпочитаете использовать [npm пакета SDK веб-карты Azure](https://www.npmjs.com/package/azure-maps-control) вместо ссылки на CDN размещенной версию пакета SDK. Это позволит уменьшить время загрузки, так как пакет SDK будет уже присутствовать на устройство пользователя и не должны загружаться во время выполнения.

## <a name="nodejs"></a>Node.js

В Node.js также поддерживаются следующие модули веб-пакета SDK:

- Модуль служб ([документации](how-to-use-services-module.md) | [модуль npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Устаревшие браузеры

Если вам нужно выбрать старых браузерах, которые могут не поддерживать или имеют ограниченную поддержку WebGL, рекомендуется использовать службы Azure Maps в сочетании с элементом управления карты с открытым исходным кодом, такие как [Листовка](https://leafletjs.com/). 


<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Листовка" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Листовка</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о SDK веб-карт Azure.

> [!div class="nextstepaction"]
> [Управление картами](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Модуль служб](how-to-use-services-module.md)
