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
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686651"
---
# <a name="web-sdk-supported-browsers"></a>Браузеры, поддерживаемые в веб-пакетах SDK

SDK веб-карты Azure предоставляет вспомогательную функцию, именуемую [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Эта функция определяет, имеет ли веб-браузер минимальный набор функций WebGL, должны поддерживать загрузку и отрисовку элемента управления картой. Вот пример того, как использовать функцию:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Классические приложения

SDK веб-карты Azure поддерживает следующие браузеры рабочего стола:

- Microsoft Edge (текущей и предыдущей версии)
- Google Chrome (текущей и предыдущей версии)
- Mozilla Firefox (текущей и предыдущей версии)
- Apple Safari (Mac OS X) (текущей и предыдущей версии)

См. также [предназначенных для предыдущих версий браузеров](#Target-Legacy-Browsers) далее в этой статье.

## <a name="mobile"></a>Мобильный

SDK веб-карты Azure поддерживает следующие браузеры мобильных устройств:

- Android
  - Текущая версия Chrome на базе Android 6.0 и более поздних версий
  - Chrome WebView на базе Android 6.0 и более поздних версий
- iOS
  - Safari Mobile в текущей и предыдущей основной номер версии iOS
  - UIWebView и WKWebView на текущим и предыдущим основной номер версии iOS
  - Текущая версия Chrome для iOS

> [!TIP]
> Если вы выполняете внедрение карты в мобильных приложениях с помощью элемента управления WebView, можно использовать [npm пакета SDK веб-карты Azure](https://www.npmjs.com/package/azure-maps-control) вместо ссылки на версию пакета SDK, который размещается на доставки содержимого Azure Сеть. Этот подход уменьшает время загрузки, так как пакет SDK уже присутствовать на устройстве пользователя и может не загружаться во время выполнения.

## <a name="nodejs"></a>Node.js

В Node.js также поддерживаются следующие модули веб-пакета SDK:

- Модуль служб ([документации](how-to-use-services-module.md) | [модуль npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Устаревшие браузеры

Может потребоваться целевой старых браузерах, которые не поддерживают WebGL или, только ограниченную поддержку для него. В таких случаях мы рекомендуем использовать службы Azure Maps вместе с элемента управления карты с открытым исходным кодом, например [Листовка](https://leafletjs.com/). Ниже приведен пример:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Листовка" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Листовка</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Управление картами](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Модуль служб](how-to-use-services-module.md)
