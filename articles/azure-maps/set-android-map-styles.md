---
title: Задание стиля схемы с помощью Azure Maps пакет SDK для Android | Карты Microsoft Azure
description: В этой статье вы узнаете о функциях, связанных со стилем Microsoft Azure Maps для пакет SDK для Android.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334356"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Задание стиля схемы с помощью Azure Maps пакет SDK для Android

В этой статье показано два способа установки стилей карт с помощью пакет SDK для Android Azure Maps. Azure Maps имеет шесть различных стилей карт для выбора. Дополнительные сведения о поддерживаемых стилях карт см. [в разделе Поддерживаемые стили карт в Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Предварительные условия

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

Приведенный выше `mapcontrol_style` атрибут устанавливает стиль отображения **grayscale_dark**. 

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