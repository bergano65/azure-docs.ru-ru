---
title: Добавление мозаичного слоя в карты Android в Azure Maps | Документация Майкрософт
description: Добавление мозаичного слоя в карту с помощью Azure Maps пакет SDK для Android
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5d5f50a38db95f6e62bdd8c51aefd5957041e682
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886615"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Добавление мозаичного слоя в карту с помощью Azure Maps пакет SDK для Android

В этой статье показано, как визуализировать мозаичный слой на карте с помощью пакет SDK для Android Azure Maps. Слои фрагментов позволяют накладывать изображения поверх фрагментов карты в Azure Maps. Дополнительные сведения о системе фрагментов Azure Maps см. в статье [Уровни увеличения и параметры сетки](zoom-levels-and-tile-grid.md).

Мозаичный слой загружает плитки с сервера. Эти изображения можно предварительно визуализировать и хранить (как и любое другое изображение) на сервере с помощью соглашения об именовании, которое слой фрагмента может распознать. Кроме того, можно воспользоваться динамической службой, которая создает изображения в режиме реального времени. Существует три разных соглашения об именовании службы мозаики, поддерживаемые Azure Maps классом Тилелайер. 

* X, Y, нотация увеличения. В зависимости от уровня увеличения, x — это столбец, а y — позиция строки фрагмента в сетке фрагментов.
* Нотация Quadkey. Сочетание данных x, y и увеличения в одном строковом значении, которое является уникальным идентификатором для фрагмента.
* Ограничивающий прямоугольник. Координаты ограничивающего прямоугольника позволяют указать образ в формате `{west},{south},{east},{north}`. Часто используется [веб-службами сопоставления (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Тилелайер — это отличный способ визуализировать большие наборы данных на карте. С его помощью можно не только создать слой фрагментов из образа, но и отобразить векторные данные в виде слоя фрагментов. Отображение векторных данных в виде слоя фрагментов позволяет элементу управления картой загрузить фрагменты, размер файлов которых может быть значительно меньше, чем у векторных данных, которые они представляют. Эта техника часто используется, когда нужно преобразовать для просмотра огромное количество строк данных на карте.

URL-адрес фрагмента, передаваемый в слой фрагментов, должен быть URL-адресом с http/https к ресурсу TileJSON или шаблоном URL-адреса фрагмента, который использует следующие параметры: 

* `{x}` — позиция X фрагмента. Также нужны `{y}` и `{z}`.
* `{y}` — позиция Y фрагмента. Также нужны `{x}` и `{z}`.
* `{z}` — уровень увеличения фрагмента. Также нужны `{x}` и `{y}`.
* `{quadkey}` — идентификатор quadkey на основе соглашения об именовании системы фрагментов Bing Maps.
* `{bbox-epsg-3857}` — строка ограничивающего прямоугольника в формате `{west},{south},{east},{north}` в системе пространственных ссылок 3857 EPSG.
* `{subdomain}` — заполнитель, вместо которого будут добавлены значения поддомена, если они указаны.

## <a name="prerequisites"></a>предварительные требования

Чтобы завершить процесс, описанный в этой статье, необходимо установить [Azure Maps пакет SDK для Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) , чтобы загрузить карту.


## <a name="add-a-tile-layer-to-the-map"></a>Добавление мозаичного слоя на карту

 В этом примере показано, как создать слой фрагментов, который указывает на набор фрагментов, использующих систему фрагментов с x, y и увеличением. Источником этого слоя фрагментов является наложение радара погоды из [лаборатории окружающей среды Университета штата Айова](https://mesonet.agron.iastate.edu/ogc/). 

Вы можете добавить мозаичный слой на карту, выполнив следующие действия.

1. Измените **разметку res > > activity_main. XML** , чтобы она выглядела так:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Скопируйте приведенный ниже фрагмент кода в `MainActivity.java` метод **OnCreate ()** класса.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Приведенный выше фрагмент кода сначала получает экземпляр элемента управления Map Azure Maps с помощью метода обратного вызова onreading **()** . Затем он создает `TileLayer` объект и передает отформатированный URL-адрес `tileUrl` элемента XYZ в параметр. Непрозрачность слоя имеет значение `0.8` , а поскольку плитки из используемой службы мозаики — 256 пикселей, эти сведения передаются `tileSize` в параметр. Затем мозаичный слой передается в Диспетчер слоев Maps.

    После добавления приведенного выше `MainActivity.java` фрагмента кода он должен выглядеть следующим образом:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
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

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
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

Если запустить приложение сейчас, на карте отобразится строка, как показано ниже:

<center>

![Линия с картой Android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о способах установки стилей карт см. в следующей статье.

> [!div class="nextstepaction"]
> [Изменение стилей карты в картах Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)