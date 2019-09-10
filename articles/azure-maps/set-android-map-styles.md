---
title: Функции стиля карт в службе Azure Maps | Документация Майкрософт
description: Сведения о функциях, связанных с Azure Maps стилем для пакет SDK для Android.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcc73c63e7c9d804d01df98551aa51b81d98d07
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844851"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Задание стиля схемы с помощью Azure Maps пакет SDK для Android

В этой статье показано два способа установки стилей карт с помощью пакет SDK для Android Azure Maps. Azure Maps имеет шесть различных стилей карт для выбора. Дополнительные сведения о поддерживаемых стилях карт см. [в разделе Поддерживаемые стили карт в Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Предварительные требования

Чтобы завершить процесс, описанный в этой статье, необходимо установить [Azure Maps пакет SDK для Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) , чтобы загрузить карту.


## <a name="set-map-style-in-the-layout"></a>Задать стиль схемы в макете

Стиль схемы можно задать в файле макета для класса действия. Измените **разметку res > > activity_main. XML**, поэтому он выглядит следующим образом:

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

Приведенный выше атрибутустанавливаетстильотображения`mapcontrol_style` grayscale_dark. 

<center>

![Style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Задание стиля схемы в классе действия

Стиль схемы можно задать в классе действия. Скопируйте следующий фрагмент кода в метод `MainActivity.java` **OnCreate ()** класса. При этом для стиля схемы будет задано значение **satellite_road_labels**.

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