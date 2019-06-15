---
title: Функции стиля карт в службе Azure Maps | Документация Майкрософт
description: Дополнительные сведения об Azure Maps стиля связанные функциональные возможности для пакета SDK для Android.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870977"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Задать стиль карты с помощью Azure Maps пакета SDK для Android

В этой статье показано два способа установки стилей карт, с помощью пакета SDK Android карт Azure. Карты Azure имеет шесть стилей различных картах. Дополнительные сведения о поддерживаемых Преобразовать стили, см. в разделе [поддерживается стилей карт в Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Технические условия

Чтобы завершить процесс, в этой статье, необходимо установить [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) загрузить карту.


## <a name="set-map-style-in-the-layout"></a>Задать стиль карты в макете

Стиль карты можно задать в файле макета для класса действия. Изменить **res > макет > activity_main.xml**, чтобы он выглядел, как показано ниже:

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

`mapcontrol_style` Приведенный выше атрибут задает стиль карты **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Задать стиль карты в классе activity

Стиль карты можно задать в классе activity. Скопируйте следующий фрагмент кода в **onCreate()** метод вашей `MainActivity.java` класса. Это установит стиль карты **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Стиль вспомогательных road метки](./media/set-android-map-styles/satellite-road-labels.png)</center>