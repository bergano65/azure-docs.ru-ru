---
title: Установите стиль карты с помощью Azure Maps Android SDK Карты Microsoft Azure
description: В этой статье вы узнаете о функциональных возможностях, связанных со стилем Microsoft Azure Maps для Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334356"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Установите стиль карты с помощью Azure Maps Android SDK

В этой статье показаны два способа настройки стилей карт с помощью Azure Maps Android SDK. Azure Maps имеет шесть различных стилей карт на выбор. Для получения дополнительной информации о стилях поддерживаемых карт смотрите [поддерживаемые стили карт в Azure Maps.](./supported-map-styles.md)


## <a name="prerequisites"></a>Предварительные требования

Чтобы завершить процесс в этой статье, необходимо установить [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) для загрузки карты.


## <a name="set-map-style-in-the-layout"></a>Установите стиль карты в макете

Вы можете установить стиль карты в файле макета для класса активности. Edit **res > макет > activity_main.xml,** так что это выглядит как один ниже:

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

Атрибут `mapcontrol_style` выше устанавливает стиль карты **для grayscale_dark.** 

<center>

![стиль grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Установите стиль карты в классе действий

Стиль карты можно установить в классе активности. Скопируйте следующий фрагмент кода в метод **onCreate()** вашего `MainActivity.java` класса. Этот код установит стиль карты для **satellite_road_labels.**

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![стиль-спутник-дорожные этикетки](./media/set-android-map-styles/satellite-road-labels.png)</center>