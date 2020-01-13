---
title: Поддерживаемые браузеры веб-пакета SDK | Карты Microsoft Azure
description: В этой статье вы узнаете о поддерживаемых браузерах для веб-пакета SDK для Microsoft Azure Maps и о том, как проверить, поддерживается ли браузер.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6a67a358d45ac61c955c26f495169f7cd8364074
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910444"
---
# <a name="web-sdk-supported-browsers"></a>Браузеры, поддерживаемые в веб-пакетах SDK

Веб-пакет SDK Azure Maps предоставляет вспомогательную функцию с именем [Atlas. support](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Эта функция определяет, имеет ли веб-браузер минимальный набор функций WebGL, необходимых для поддержки загрузки и отрисовки элемента управления картой. Ниже приведен пример использования функции.

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Классические приложения

Веб-пакет SDK Azure Maps поддерживает следующие браузеры для настольных систем:

- Microsoft ребро (Текущая и Предыдущая версии)
- Google Chrome (Текущая и Предыдущая версии)
- Mozilla Firefox (Текущая и Предыдущая версии)
- Apple Safari (Mac OS X) (Текущая и Предыдущая версии)

См. также в разделе [устаревшие браузеры](#Target-Legacy-Browsers) ниже в этой статье.

## <a name="mobile"></a>Мобильные приложения

Веб-пакет SDK Azure Maps поддерживает следующие мобильные браузеры:

- Android
  - Текущая версия Chrome на Android 6,0 и более поздних версиях
  - Chrome WebView в Android 6,0 и более поздних версиях
- iOS
  - Mobile Safari в текущей и предыдущей основной версии iOS
  - Уивебвиев и Вквебвиев в текущей и предыдущей основной версии iOS
  - Текущая версия Chrome для iOS

> [!TIP]
> При внедрении схемы в мобильное приложение с помощью элемента управления WebView можно использовать [пакет NPM веб-пакета sdk Azure Maps](https://www.npmjs.com/package/azure-maps-control) вместо ссылки на версию пакета SDK, размещенного в сети доставки содержимого Azure. Такой подход сокращает время загрузки, так как пакет SDK уже находится на устройстве пользователя и не требуется загружать его во время выполнения.

## <a name="nodejs"></a>Node.js

В Node. js также поддерживаются следующие модули веб-пакета SDK:

- Модуль служб ([документация](how-to-use-services-module.md) | [модуль NPM](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Целевые браузеры прежних версий

Вы можете использовать более старые браузеры, которые не поддерживают WebGL или имеют только ограниченную поддержку. В таких случаях рекомендуется использовать службы Azure Maps вместе с элементом управления картой с открытым кодом, например [леафлет](https://leafletjs.com/). Ниже приведен пример:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Леафлет" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Дополнительные сведения см. в разделе Перо <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + леафлет</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о веб-пакете SDK для Azure Maps:

> [!div class="nextstepaction"]
> [Управление картами](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Модуль служб](how-to-use-services-module.md)
