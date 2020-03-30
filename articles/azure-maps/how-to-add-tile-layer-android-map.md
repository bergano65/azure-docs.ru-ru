---
title: Добавить слой плитки к картам Android Карты Microsoft Azure
description: В этой статье вы узнаете, как визуализировать слой плитки на карте с помощью SDK Android SDK Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335563"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Добавьте слой плитки на карту с помощью Android SDK Azure Maps

В этой статье показано, как визуализировать слой плитки на карте с помощью Azure Maps Android SDK. Слои фрагментов позволяют накладывать изображения поверх фрагментов карты в Azure Maps. Дополнительные сведения о системе фрагментов Azure Maps см. в статье [Уровни увеличения и параметры сетки](zoom-levels-and-tile-grid.md).

Слой плитки загружается в плитки с сервера. Эти изображения могут быть предварительно отрисованы и сохранены, как и любое другое изображение на сервере, используя конвенцию именования, которую понимает слой плитки. Или эти изображения могут быть отображаются с помощью динамической службы, которая генерирует изображения в режиме реального времени. Существует три различных конвенции по именованию услуг плитки, поддерживаемые классом Azure Maps TileLayer:

* X, Y, нотация увеличения. В зависимости от уровня увеличения, x — это столбец, а y — позиция строки фрагмента в сетке фрагментов.
* Нотация Quadkey. Сочетание данных x, y и увеличения в одном строковом значении, которое является уникальным идентификатором для фрагмента.
* Ограничивающий прямоугольник. Координаты ограничивающего прямоугольника позволяют указать образ в формате `{west},{south},{east},{north}`. Часто используется [веб-службами сопоставления (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Класс TileLayer — отличный способ визуализировать большие объемы данных на карте. С его помощью можно не только создать слой фрагментов из образа, но и отобразить векторные данные в виде слоя фрагментов. Отображение векторных данных в виде слоя фрагментов позволяет элементу управления картой загрузить фрагменты, размер файлов которых может быть значительно меньше, чем у векторных данных, которые они представляют. Эта техника часто используется, когда нужно преобразовать для просмотра огромное количество строк данных на карте.

URL-адрес фрагмента, передаваемый в слой фрагментов, должен быть URL-адресом с http/https к ресурсу TileJSON или шаблоном URL-адреса фрагмента, который использует следующие параметры: 

* `{x}` — позиция X фрагмента. Также нужны `{y}` и `{z}`.
* `{y}` — позиция Y фрагмента. Также нужны `{x}` и `{z}`.
* `{z}` — уровень увеличения фрагмента. Также нужны `{x}` и `{y}`.
* `{quadkey}` — идентификатор quadkey на основе соглашения об именовании системы фрагментов Bing Maps.
* `{bbox-epsg-3857}` — строка ограничивающего прямоугольника в формате `{west},{south},{east},{north}` в системе пространственных ссылок 3857 EPSG.
* `{subdomain}`- Заполнитель значений субдоменов, если указано значение субдомена.

## <a name="prerequisites"></a>Предварительные требования

Чтобы завершить процесс в этой статье, необходимо установить [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) для загрузки карты.


## <a name="add-a-tile-layer-to-the-map"></a>Добавить слой плитки на карту

 В этом примере показано, как создать слой плитки, который указывает на набор плиток. Эти плитки используют систему плитки "x, y, зум". Источником этого слоя фрагментов является наложение радара погоды из [лаборатории окружающей среды Университета штата Айова](https://mesonet.agron.iastate.edu/ogc/). 

Вы можете добавить слой плитки на карту, выполнив приведенные ниже шаги.

1. Edit **res > макет > activity_main.xml,** так что это выглядит как один ниже:

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

2. Скопируйте следующий фрагмент кода ниже в метод `MainActivity.java` **onCreate()** вашего класса.

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
    
    Фрагмент кода выше сначала получает экземпляр управления картой Azure Maps с помощью метода обратного вызова **onReady()** обратный вызов. Затем он `TileLayer` создает объект и передает отформатированный `tileUrl` URL-адрес **плитки xyz** в опцион. Непрозрачность слоя устанавливается, `0.8` и так как плитки из плитки службы используются 256 пикселей плитки, эта информация передается в опцию. `tileSize` Слой плитки затем передается в менеджер слоя карт.

    После добавления фрагмента кода `MainActivity.java` выше, ваш должен выглядеть как один ниже:
    
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

Если вы запустите приложение сейчас, вы должны увидеть строку на карте, как показано ниже:

<center>

![Линия карты Android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующую статью, чтобы узнать больше о способах настройки стилей карт

> [!div class="nextstepaction"]
> [Изменение стилей карт на картах Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)