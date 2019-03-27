---
title: Поддерживаемые веб-браузеры - "карты Azure" | Документация Майкрософт
description: Дополнительные сведения о поддерживаемых веб-браузерах для SDK веб-карт Azure
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501276"
---
# <a name="supported-web-browsers"></a>Поддерживаемые веб-браузеры

SDK веб-карты Azure предоставляет вспомогательную функцию [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) для обнаружения, если браузер имеет минимальное приведенные WebGL компоненты для поддержки загрузки и Подготовка к просмотру элемента управления картой. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

SDK веб-карты Azure поддерживает следующие браузеры.

## <a name="desktop"></a>Классические приложения

- Текущие и предыдущие версии Microsoft Edge 
- Текущие и предыдущие версии Chrome 
- Текущие и предыдущие версии Firefox 
- Текущие и предыдущие версии Safari (Mac OS X) 

См. также [предназначенных для предыдущих версий браузеров](#Target-Legacy-Browsers).

## <a name="mobile"></a>Мобильные приложения

-  Android
    * Текущая версия Chrome в Android 6.0 +
    * Chrome WebView Android 6.0 +
- iOS
    * Safari Mobile в текущей и предыдущей основной номер версии iOS
    * UIWebView и WKWebView на текущим и предыдущим основной номер версии iOS
    * Текущая версия Chrome для iOS

> [!TIP]
> Если вы внедряете карту внутри элемента управления WebView представления мобильного приложения, возможно, с помощью [npm пакета SDK Azure Maps веб-](https://www.npmjs.com/package/azure-maps-control) предпочтительнее ссылается на версию пакета SDK CDN размещенной. Это позволит уменьшить время загрузки, так как пакет SDK будет уже присутствовать на устройство пользователя и не должны загружаться во время выполнения.

## <a name="nodejs"></a>Node.js

В Node.js также поддерживаются следующие модули веб-пакета SDK:

- Модуль служб ([документации](how-to-use-services-module.md) | [модуль npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Устаревшие браузеры

Если вам нужно выбрать браузеры прежних версий, которые могут не поддерживать или имеют ограниченную поддержку WebGL, рекомендуется использовать службы Azure Maps в сочетании с элементом управления карты с открытым исходным кодом, такие как [Листовка](https://leafletjs.com/). 

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Листовка" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Листовка</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>