---
title: Веб-SDK поддерживается браузеров Карты Microsoft Azure
description: В этой статье вы узнаете об поддерживаемых браузерах для Microsoft Azure Maps Web SDK и о том, как проверить, является ли браузер поддерживаемым браузером.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988793"
---
# <a name="web-sdk-supported-browsers"></a>Браузеры, поддерживаемые в веб-пакетах SDK

Лазурные карты Web SDK обеспечивает вспомогательную функцию под названием [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Эта функция определяет, имеет ли веб-браузер минимальный набор функций WebGL, необходимых для поддержки загрузки и визуализации управления картой. Вот пример того, как использовать функцию:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Рабочий стол

Azure Maps Web SDK поддерживает следующие настольные браузеры:

- Microsoft Edge (текущая и предыдущая версия)
- Google Chrome (текущая и предыдущая версия)
- Mozilla Firefox (текущая и предыдущая версия)
- Apple Safari (Mac OS X) (текущая и предыдущая версия)

Смотрите также [Целевые устаревшие браузеры](#Target-Legacy-Browsers) позже в этой статье.

## <a name="mobile"></a>Мобильный

Azure Maps Web SDK поддерживает следующие мобильные браузеры:

- Android
  - Текущая версия Chrome на Android 6.0 и позже
  - Chrome WebView на Android 6.0 и позже
- iOS
  - Мобильное safari на текущей и предыдущей основной версии iOS
  - UIWebView и WKWebView на текущей и предыдущей основной версии iOS
  - Текущая версия Chrome для iOS

> [!TIP]
> Если вы встраиваете карту в мобильное приложение с помощью управления WebView, вы можете использовать [пакет npm Web SDK Azure Maps](https://www.npmjs.com/package/azure-maps-control) вместо ссылки на версию SDK, размещенную в сети доставки содержимого Azure. Такой подход сокращает время загрузки, поскольку SDK уже находится на устройстве пользователя и не нуждается в загрузке во время выполнения.

## <a name="nodejs"></a>Node.js

Следующие модули Web SDK также поддерживаются в Node.js:

- Модуль услуг[(модуль документации](how-to-use-services-module.md) | [npm)](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Целевые устаревшие браузеры

Возможно, вы захотите настроить таргетинг на старые браузеры, которые не поддерживают WebGL или имеют лишь ограниченную поддержку. В таких случаях мы рекомендуем использовать службы Azure Maps вместе с управлением картой с открытым исходным кодом, например [листовкой.](https://leafletjs.com/) Ниже приведен пример:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Лазурные Карты и листовки" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите Карты Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure и листовки</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о веб-SDK Azure Maps:

> [!div class="nextstepaction"]
> [Элемент управления картой](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Модуль служб](how-to-use-services-module.md)
