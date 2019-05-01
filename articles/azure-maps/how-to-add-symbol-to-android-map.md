---
title: Добавление карты слоя символ для Android в Azure сопоставляет | Документация Майкрософт
description: Добавление символов в карту с помощью Azure Maps пакета SDK для Android
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871082"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Добавьте символ слой на карту с помощью Azure Maps пакета SDK для Android

В этой статье показано, как для подготовки к просмотру как слой символ на карте с помощью пакета SDK Android карты Azure точки данных из источника данных.

## <a name="prerequisites"></a>Технические условия

Чтобы полностью выполнить действия, описанные в этой статье, необходимо установить [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) загрузить карту.

## <a name="add-a-symbol-layer"></a>Добавление слоя символов

Чтобы добавить маркер на карте с помощью слоя символ, выполните следующие действия:

1. Изменить **res** > **макета** > **activity_main.xml** так он выглядит как следующий код XML:
    
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

2. Скопируйте следующий фрагмент кода в **onCreate()** метод вашей `MainActivity.java` класса.

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
    
    Приведенный выше фрагмент кода сначала получает значение "карты Azure" карты управления экземпляров с помощью **onReady()** метод обратного вызова. Затем создается объект источника данных с помощью **DataSource** класса и добавляет его к схеме. Затем он добавляет **функция** содержащий точек геометрического объекта к нему. Изображение красной маркера затем задается в качестве значка для символа. Объект **слой символ** использует текст или значки, для визуализации данных на основе точки в оболочку в источнике данных в качестве символа на карте. Затем создается слой символов и источника данных передается в него для подготовки к просмотру и добавляется в слои карты.
    
    После добавления в приведенном выше фрагменте кода вашей `MainActivity.java` должен выглядеть, как показано ниже:
    
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
    
На этом этапе при запуске приложения вы увидите маркер на карте, как показано ниже:

<center>

![ПИН-код карты Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Дальнейшие действия

Чтобы добавить дополнительных материалов на карту, см. в разделе:

> [!div class="nextstepaction"]
> [Добавление фигур в Android карту](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)