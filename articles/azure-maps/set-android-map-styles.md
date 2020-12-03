---
title: Задание стиля схемы с помощью Azure Maps пакет SDK для Android
description: Узнайте о двух способах настройки стиля схемы. См. сведения об использовании Microsoft Azure Maps пакет SDK для Android в файле макета или классе действия для настройки стиля.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532489"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Задание стиля схемы с помощью Azure Maps пакет SDK для Android

В этой статье показано, как задать стили карт с помощью пакет SDK для Android Azure Maps. Azure Maps имеет шесть различных стилей карт для выбора. Дополнительные сведения о поддерживаемых стилях карт см. [в разделе Поддерживаемые стили карт в Azure Maps](./supported-map-styles.md).

## <a name="prerequisites"></a>Предварительные требования

1. [Создайте учетную запись службы Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Получите первичный ключ подписки](quick-demo-map-app.md#get-the-primary-key-for-your-account), который иногда называется первичным ключом или ключом подписки.
3. Скачайте и установите [пакет SDK для Android Azure Maps](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Задать стиль схемы в макете

Стиль схемы можно задать в файле макета для класса действия. Измените `res > layout > activity_main.xml` , поэтому он выглядит следующим образом:

```XML
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

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps, изображение на карте, показывающее стиль как grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Задание стиля схемы в классе MainActivity

Стиль схемы также можно задать в классе MainActivity. Откройте `java > com.example.myapplication > MainActivity.java` файл и скопируйте следующий фрагмент кода в метод **OnCreate ()** . Этот код задает для стиля отображения значение **satellite_road_labels**.

>[!WARNING]
>Возможно, Android Studio не импортировали необходимые классы.  В результате код будет содержать неразрешимые ссылки. Чтобы импортировать необходимые классы, просто наведите указатель мыши на каждую неразрешенную ссылку и нажмите `Alt + Enter` (Option + Return на компьютере Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps, изображение на карте, показывающее стиль как satellite_road_labels":::