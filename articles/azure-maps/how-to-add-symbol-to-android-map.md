---
title: Добавление слоя символов в карты Android | Карты Microsoft Azure
description: Узнайте, как добавить маркер к карте. См. пример, использующий Azure Maps пакет SDK для Android для добавления слоя символов, который содержит данные на основе точек из источника данных.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: acd5f06a5383308ce736f2860810ebee7e5bce28
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897115"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Добавление слоя символов в карту с помощью Azure Maps пакет SDK для Android

В этой статье показано, как преобразовать данные точек из источника данных в качестве слоя символов на карте с помощью Azure Maps пакет SDK для Android.

## <a name="prerequisites"></a>Предварительные требования

Чтобы полностью выполнить действия, описанные в этой статье, необходимо установить [Azure Maps пакет SDK для Android](./how-to-use-android-map-control-library.md) , чтобы загрузить карту.

## <a name="add-a-symbol-layer"></a>Добавление слоя символов

Чтобы добавить маркер на карту с помощью слоя символов, выполните следующие действия:

1. Измените макет " **RES**  >  **layout**  >  " **activity_main.xml** так, чтобы он выглядел как следующий XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Скопируйте следующий фрагмент кода в метод **OnCreate ()** `MainActivity.java` класса.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Приведенный выше фрагмент кода сначала получает экземпляр элемента управления Map Azure Maps с помощью метода обратного вызова **Onreading ()** . Затем он создает объект источника данных с помощью класса **DataSource** и добавляет его к сопоставлению. Затем он добавляет в него **компонент** , содержащий геометрию точки. Затем изображение Красного маркера устанавливается в качестве значка для символа. **Слой символов** использует текст или значки для отрисовки данных на основе точек, инкапсулированных в источнике данных, в виде символа на карте. Затем создается слой символов, и источник данных передается ему для подготовки к просмотру, а затем добавляется к слоям на карте.
    
    После добавления приведенного выше фрагмента кода он `MainActivity.java` должен выглядеть следующим образом:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```
    
На этом этапе при запуске приложения на карте должен отобразиться маркер, как показано ниже:

<center>

![Метка на карте Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> По умолчанию слои символов оптимизируют отрисовку символов путем скрытия перекрывающихся символов. При изменении масштаба скрытые символы становятся видимыми. Чтобы отключить эту функцию и вывести все символы в любое время, установите `iconAllowOverlap` параметр в значение `true` .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о добавлении дополнительных материалов на карту см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление фигур на карту Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Отображение сведений о компоненте](display-feature-information-android.md)