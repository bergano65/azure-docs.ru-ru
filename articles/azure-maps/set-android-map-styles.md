---
title: Задание стиля схемы с помощью Azure Maps пакет SDK для Android | Карты Microsoft Azure
description: Узнайте о двух способах настройки стиля схемы. Сведения об изменении стиля см. в разделе Использование Azure Maps пакет SDK для Android в файле макета или в классе действия.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4e37ae82b46ce06162d0a67d74af54cddaf3f6eb
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88030951"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Задание стиля схемы с помощью Azure Maps пакет SDK для Android

В этой статье показано два способа установки стилей карт с помощью пакет SDK для Android Azure Maps. Azure Maps имеет шесть различных стилей карт для выбора. Дополнительные сведения о поддерживаемых стилях карт см. [в разделе Поддерживаемые стили карт в Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Предварительные требования

Чтобы завершить процесс, описанный в этой статье, необходимо установить [Azure Maps пакет SDK для Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) , чтобы загрузить карту.


## <a name="set-map-style-in-the-layout"></a>Задать стиль схемы в макете

Стиль схемы можно задать в файле макета для класса действия. Измените **разметку res > > activity_main.xml**, поэтому она выглядит следующим образом:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

Приведенный `mapcontrol_style` выше атрибут устанавливает стиль отображения **grayscale_dark**. 

<center>

![стиль — grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Задание стиля схемы в классе действия

Стиль схемы можно задать в классе действия. Скопируйте следующий фрагмент кода в метод **OnCreate ()** `MainActivity.java` класса. Этот код установит стиль отображения **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Style-Спутниковое-Road-Labels](./media/set-android-map-styles/satellite-road-labels.png)</center>