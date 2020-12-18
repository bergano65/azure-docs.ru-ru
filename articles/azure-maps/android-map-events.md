---
title: Обработку событий сопоставления в картах Android | Карты Microsoft Azure
description: Узнайте, какие события срабатывают при взаимодействии пользователей с картами. Просмотр списка всех поддерживаемых событий карт. См. раздел Использование пакет SDK для Android Azure Maps для управления событиями.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681667"
---
# <a name="interact-with-the-map-android-sdk"></a>Взаимодействие с картой (пакет SDK для Android)

В этой статье показано, как использовать диспетчер событий Maps.

## <a name="interact-with-the-map"></a>Взаимодействие с картой

Эта схема управляет всеми событиями с помощью его `events` Свойства. В следующей таблице перечислены все поддерживаемые события сопоставлений.

| Событие                  | Формат обработчика событий | Описание |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Возникает после отображения последнего кадра до того, как схема переходит в состояние простоя:<ul><li>Нет выполняющихся переходов с камеры.</li><li>Все запрошенные плитки загружены.</li><li>Все анимации исчезания/перехода завершены.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Возникает повторно во время анимированного перехода из одного представления в другое в результате взаимодействия с пользователем или методов. |
| `OnCameraMoveCanceled` | `()`                 | Возникает при отмене запроса на перемещение к камере. |
| `OnCameraMoveStarted`  | `(int reason)`       | Возникает непосредственно до начала перехода карты из одного представления в другое в результате взаимодействия с пользователем или методов. `reason`Аргумент прослушивателя событий возвращает целочисленное значение, которое предоставляет сведения о том, как было инициировано перемещение камеры. Ниже приведен список возможных причин.<ul><li>1: жест</li><li>2: анимация разработчика</li><li>3: анимация API</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Возникает при нажатии и отпускании кнопки на карте в той же точке. |
| `OnFeatureClick`       | `(List<Feature>)`    | Возникает при нажатии и освобождении схемы в той же точке компонента.  |
| `OnLongClick`          | `(double lat, double lon)` | Возникает при нажатии на карту, в течение некоторого времени, после чего освобождается в той же точке на карте. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Возникает при нажатии на карту, в течение некоторого времени, а затем в той же точке компонента. |
| `OnReady`              | `(AzureMap map)`     | Возникает при первоначальной загрузке схемы или при изменении ориентации приложения и загрузке минимально требуемых ресурсов карт и готовности схемы к взаимодействию с. |

В следующем коде показано, как добавить `OnClick` события, `OnFeatureClick` и `OnCameraMove` в карту.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

Дополнительные сведения см. в разделе [Навигация](how-to-use-android-map-control-library.md#navigating-the-map) по документации по карте о взаимодействии с событиями Map и Trigger.

## <a name="scope-feature-events-to-layer"></a>События области действия для слоя

При добавлении `OnFeatureClick` событий или `OnLongFeatureClick` к сопоставлению идентификатор слоя может передаваться в качестве второго параметра. При передаче идентификатора слоя событие срабатывает только в случае возникновения события в этом слое. События, областью которых является слои, поддерживаются в слоях "символ", "пузырьковая", "линия" и "Многоугольник".

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

## <a name="next-steps"></a>Дальнейшие действия

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Перемещение по карте](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Добавление слоя символов](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](how-to-add-shapes-to-android-map.md)
