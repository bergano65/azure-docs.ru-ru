---
title: Поддерживаемые браузеры веб-пакета SDK — Azure Maps | Документация Майкрософт
description: Дополнительные сведения о поддерживаемых браузерах для Azure Maps веб-пакета SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: d867e9a1afcb495aee7e8b0e0b3b001104f48a4c
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844824"
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

## <a name="desktop"></a>Рабочий стол

Веб-пакет SDK Azure Maps поддерживает следующие браузеры для настольных систем:

- Microsoft ребро (Текущая и Предыдущая версии)
- Google Chrome (Текущая и Предыдущая версии)
- Mozilla Firefox (Текущая и Предыдущая версии)
- Apple Safari (Mac OS X) (Текущая и Предыдущая версии)

См. также в разделе [устаревшие браузеры](#Target-Legacy-Browsers) ниже в этой статье.

## <a name="mobile"></a>Мобильный

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

- Модуль служб ([Документация](how-to-use-services-module.md) | [NPM Module](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Целевые браузеры прежних версий

Вы можете использовать более старые браузеры, которые не поддерживают WebGL или имеют только ограниченную поддержку. В таких случаях рекомендуется использовать службы Azure Maps вместе с элементом управления картой с открытым кодом, например [леафлет](https://leafletjs.com/). Ниже приведен пример:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Леафлет" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел "перо <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + леафлет</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () в <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о веб-пакете SDK для Azure Maps:

> [!div class="nextstepaction"]
> [Управление картами](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Модуль служб](how-to-use-services-module.md)
