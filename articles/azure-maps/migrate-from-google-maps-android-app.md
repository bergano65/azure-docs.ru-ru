---
title: Перенос приложения Android | Документация Майкрософт
description: Руководство по переносу приложения Android из Google Maps в Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481403"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Перенос приложения Android из Google Maps

Azure Maps пакет SDK для Android имеет интерфейс API, который очень похож на веб-пакет SDK. Если вы разработали с одним из этих пакетов SDK, применяются многие из них, рекомендации и архитектуры, и вы сможете легко передавать свои знания из одного в другой.

Azure Maps пакет SDK для Android поддерживает минимальную версию API для Android 21: Android 5.0.0 (без описания операций).

Все примеры, представленные в Java, но Котлин также можно использовать с пакет SDK для Androidом Azure Maps.

Дополнительные сведения о разработке с помощью этого пакета SDK см. в [руководствах по Azure Maps пакет SDK для Android](how-to-use-android-map-control-library.md) .

## <a name="load-a-map"></a>Загрузка схемы

Загрузка схемы в приложение Android с помощью Google или Azure Maps состоит из многих тех же этапов. При использовании любого из пакетов SDK необходимо:

- Получите ключ API или подписки для доступа к любой из платформ.
- Добавьте XML-код в действие, чтобы указать, где должна быть визуализирована схема и как она должна быть размещена.
- Пересылка всех методов жизненного цикла из действия, содержащего представление Map, в соответствующие объекты в классе Map. В частности, необходимо переслать следующие методы:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Дождитесь готовности схемы, прежде чем пытаться получить к ней доступ программным способом.

**До: карты Google**

Чтобы отобразить карту с помощью пакета SDK для Google Maps для Android, необходимо выполнить следующие действия.

1.  Убедитесь, что службы Google Play установлены.
2.  Добавьте зависимость для службы Google Maps в файл **gradle. Build** модуля: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Добавьте ключ API Google Maps в раздел "приложение" **карты Google\_Maps\_файл API. XML** :
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Добавьте фрагмент-карту в основное действие:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  В файле **MainActivity. Java** потребуется добавить Imports для пакета SDK для Google Maps. Пересылка всех методов жизненного цикла из действия, содержащего представление Map, в соответствующие объекты в классе Map. Экземпляр `MapView` можно извлечь из фрагмента Map с помощью метода `getMapAsync(OnMapReadyCallback)`. `MapView` автоматически инициализирует систему карт и представление. Измените файл **MainActivity. Java** следующим образом:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

При запуске в приложении элемент управления картой будет загружаться следующим образом.

<center>

![простых](media/migrate-google-maps-android-app/simple-google-maps.png)</center>ных карт Google

**После: Azure Maps**

Чтобы отобразить карту с помощью пакета SDK для Azure Maps для Android, необходимо выполнить следующие действия.

1. Откройте файл **Build. gradle** верхнего уровня и добавьте следующий код в раздел **все проекты**, **репозитории** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Обновите **приложение/сборку. gradle** и добавьте в него следующий код:
    
    1. Убедитесь, что **minSdkVersion** проекта находится в API 21 или более поздней версии.

    2. Добавьте следующий код в раздел Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Обновите блок зависимостей и добавьте новую строку зависимости реализации для последней Azure Maps пакет SDK для Android:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Пакет SDK для Android Azure Maps регулярно обновляется и улучшается. Чтобы получить последнюю версию Azure Mapsной реализации, можно просмотреть документацию по [управлению картой Android](how-to-use-android-map-control-library.md) . Кроме того, можно задать номер версии от "0,2" до "0 +", чтобы он всегда указывал на последнюю версию.
    
    4. Перейдите в **файл** на панели инструментов и щелкните **синхронизировать проект с файлами Gradle**.
3. Добавьте фрагмент-карту в основное действие (\> макет RES \> действия\_Main. XML):
    
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
            />
    </FrameLayout>
    ```

4. В файле **MainActivity. Java** потребуется следующее:
    
    * Добавление импорта для пакета SDK для Azure Maps
    * Настройка сведений для проверки подлинности Azure Maps
    * получение экземпляра элемента управления Map в методе **OnCreate**

    Настройка сведений о проверке подлинности для класса `AzureMaps` глобально с помощью методов `setSubscriptionKey` или `setAadProperties` делает его ненужным, поэтому вам не придется добавлять данные проверки подлинности в каждое представление. 

    Элемент управления картой содержит собственные методы жизненного цикла для управления жизненным циклом OpenGL Android, который должен вызываться непосредственно из содержащего действия. Чтобы приложение было правильно, вызовите методы жизненного цикла элемента управления Map, переопределите следующие методы жизненного цикла в действии, содержащем элемент управления картой, и вызовите соответствующий метод управления картой. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Измените файл **MainActivity. Java** следующим образом:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

При запуске приложения элемент управления картой будет загружаться следующим образом.

<center>

![простой Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Обратите внимание, что Azure Maps элемент управления поддерживает более масштабное отображение и предоставляет больше представления мира.

> [!TIP]
> При использовании эмулятора Android в Windows карта может не отображаться из-за конфликтов с видеорендерингом OpenGL и программным ускорением. Ниже описана проблема, которая решает проблему. Откройте диспетчер AVD и выберите виртуальное устройство для изменения. В разделе **Эмуляция производительности** задайте для параметра **графика** значение **оборудование**.

## <a name="localizing-the-map"></a>Локализация схемы

Если ваша аудитория распределена по нескольким странам или говорят на разных языках, локализация важна.

**До: карты Google**

Язык Map можно задать в методе `onCreate` основного действия, добавив следующий код перед установкой представления контекста для отображения. Ниже приведено ограничение языка на французский с использованием кода языка fr.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Ниже приведен пример использования Google Maps с языком, имеющим значение fr.

<center>

![](media/migrate-google-maps-android-app/google-maps-localization.png)</center> локализации Google Maps

**После: Azure Maps**

Azure Maps предоставляет три разных способа настройки языкового и регионального представления для схемы. Первый вариант — передать сведения о языке и региональном представлении в класс `AzureMaps`, используя статические методы `setLanguage` и `setView` глобально. Это позволит задать язык по умолчанию и региональные представления для всех элементов управления Azure Maps, загруженных в приложение. Ниже приведено ограничение языка на французский с использованием кода языка fr-FR.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Вторым вариантом является передача языка и Просмотр сведений в XML-код элемента управления Map.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Третий вариант заключается в программном задании языка и регионального представления карты с помощью метода Map `setStyle`. Это можно сделать в любое время, чтобы изменить язык и региональные представления на карте.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Ниже приведен пример Azure Maps с языком, имеющим значение fr-FR.

<center>

![](media/migrate-google-maps-android-app/azure-maps-localization.png)</center> локализации Azure Maps

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="setting-the-map-view"></a>Настройка представления карт

Динамические карты в Azure и Google Maps можно программно перемещать в новые географические расположения, вызывая соответствующие методы. В приведенных ниже примерах показано, как сделать так, чтобы карта отображала вспомогательные аэрофотосъемки изображения, Центрировать карту по расположению с координатами (Широта: 35,0272, Долгота:-111,0225) и измените уровень масштабирования на 15 в Google Maps.

> [!NOTE]
> В Google Maps используются плитки 256 пикселей в измерениях, а в Azure Maps используется плитка размером 512 пикселей. Это сокращает количество сетевых запросов, необходимых, Azure Maps для загрузки той же области карты, что и Google Maps. Тем не менее, из-за того, что Пирамидальная диаграмма работает в элементах управления карты, крупные плитки в Azure Maps означает, что для достижения этой же видимой области в качестве карты в Google Maps необходимо вычесть масштаб, используемый в картах Google, на 1 при использовании Azure Maps.

**До: карты Google**

Камеру элемента управления карты Google Maps можно переместить программным способом с помощью метода `moveCamera`, который позволяет указать центр карты и уровень масштабирования. Для изменения типа отображаемой схемы можно использовать метод `setMapType`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![представление набора Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**После: Azure Maps**

Как отмечалось ранее, чтобы получить ту же видимую область в Azure Maps, вычтите уровень масштабирования, используемый в картах Google по одному, в данном случае используйте уровень масштабирования 14.

Начальное представление Map можно задать в атрибутах XML для элемента управления картой.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Представление карты можно обновить программно с помощью методов Map `setCamera` и `setStyle`.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![представление набора Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Дополнительные ресурсы:**

- [Поддерживаемые стили карт](supported-map-styles.md)

## <a name="adding-a-marker"></a>Добавление маркера

Данные точек часто отображаются на карте с помощью изображения на карте. Эти образы часто называются маркерами, канцелярскими кнопками, ПИН-символами или знаками. В следующих примерах данные точки визуализируются в виде маркеров на карте в (Широта: 51,5, Долгота:-0,2).

**До: карты Google**

При использовании Google Maps маркеры добавляются с помощью метода Maps `addMarker`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

](media/migrate-google-maps-android-app/google-maps-marker.png)</center> маркера ![Google Maps

**После: Azure Maps**

В Azure Maps данные точки можно отобразить на карте, сначала добавив данные в источник данных, а затем подключив этот источник данных к слою символов. Источник данных оптимизирует управление пространственными данными в элементе управления картой, а слой символов определяет способ отрисовки точечных данных с помощью в виде изображения или текста.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![маркер Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Добавление пользовательского маркера

Пользовательские образы можно использовать для представления точек на карте. На следующем рисунке в приведенных ниже примерах используется пользовательский образ для отображения точки на карте в (Широта: 51,5, Долгота:-0,2) и смещение позиции маркера таким образом, чтобы точка значка канцелярской точки совпадала с правильной позицией на карте.

<center>

![изображение желтой канцелярской кнопки](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
илв\_ая кнопка. png</center>

В обоих примерах приведенное выше изображение добавляется в папку для рисования ресурсов приложений.

**До: карты Google**

С помощью Google Maps пользовательские образы можно использовать для маркеров, загружая их с помощью параметра `icon` маркера. Чтобы согласовать точку изображения с координатами, можно использовать параметр `anchor`. Привязка задается относительно измерений изображения, в данном случае 0,2 единиц в ширину и 1 единица измерения.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center> настраиваемый маркер ![Google Maps

**После: Azure Maps**

Слои символов в Azure Maps поддерживают также пользовательские образы, но сначала необходимо загрузить образ в карту ресурсов и назначить уникальный идентификатор. Затем слой символов может ссылаться на этот идентификатор. Символ может быть смещен в соответствии с требуемой точкой изображения с помощью параметра `iconOffset`. Обратите внимание, что смещение значка находится в пикселях. По умолчанию смещение задается относительно нижнего края изображения, но это можно изменить с помощью параметра `iconAnchor`. В этом примере параметру `iconAnchor` присваивается значение `"center"` и используется смещение значка для перемещения изображения с пятью пикселами вправо и 15 пикселов в соответствии с точкой изображения канцелярской кнопки.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![настраиваемый маркер Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Добавление ломаной линии

Ломаные линии используются для представления линий или траекторий на карте. В следующих примерах показано, как создать пунктирную ломаную линию на карте.

**До: карты Google**

С помощью карты Google можно создать ломаную линию с помощью класса `PolylineOptions` и добавить их в карту с использованием метода `addPolyline`. Цвет обводки можно задать с помощью параметра `color`. ширина штриха задается с помощью параметра width, а массив штрихов штриха можно задать с помощью параметра `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-polyline.png)</center> ломаной линии Google Maps

**После: Azure Maps**

В Azure Maps ломаные объекты называются объектами LineString или MultiLineString. Эти объекты можно добавлять в источник данных и подготавливать к просмотру с помощью слоя линий. Обратите внимание, что единицы измерения толщины штриха и тире массива "пиксель" выводятся в соответствие с Azure Maps веб-пакетом SDK в, используя те же значения в обоих пакетах SDK, что дает одинаковые результаты.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure Maps Ломаная линия](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Добавление многоугольника

Многоугольники используются для представления области на карте. В следующих примерах показано, как создать многоугольник, который образует треугольник на основе координаты центральной координаты на карте.

**До: карты Google**

С помощью карты Google можно создать многоугольник с помощью класса `PolygonOptions` и добавить его в карту, используя метод `addPolygon`. Цвета заливки и обводки можно задать с помощью параметра `fillColor` и `strokeColor`. Толщина штриха задается с помощью параметра `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-polygon.png)</center> многоугольников Google Maps

**После: Azure Maps**

В Azure Maps объекты многоугольников и многоугольных объектов можно добавить в источник данных и визуализировать на карте с помощью слоев. Площадь многоугольника может быть визуализирована на слое многоугольников. Контур многоугольника можно визуализировать с помощью слоя линий. Обратите внимание, что единицы измерения толщины штриха и тире массива "пиксель" выводятся в соответствие с Azure Maps веб-пакетом SDK в, используя те же значения в обоих пакетах SDK, что дает одинаковые результаты.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure Maps многоугольник](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Наложение мозаичного слоя

Мозаичные слои, также известные как наложение изображений в Google Maps, позволяют накладывать изображения слоев, разбитые на небольшие мозаичные изображения, которые соответствуют системе разбиения на карты. Это распространенный способ наложения изображений слоев или очень больших наборов данных.

В приведенных ниже примерах накладывается мозаичный слой погоды из Айова окружающей среды Месонет Айова штата университета. Размер плиток составляет 256 пикселей.

**До: карты Google**

С помощью карты Google можно перекрыть мозаичный слой на карте, используя класс `TileOverlayOptions` и добавленный на карту с помощью метода `addTileLauer`. Чтобы сделать плитки полупрозрачными, параметр `transparency` установлен в значение 0,2 или на 20% прозрачно.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center> мозаичного слоя Google Maps

**После: Azure Maps**

В Azure Maps мозаичный слой может быть добавлен на карту во многом так же, как и любой другой слой. Отформатированный URL-адрес с заполнителями x, y и Zoom; `{x}`, `{y}`, `{z}` соответственно, используется для указания уровню доступа к плиткам. Мозаичные слои в Azure Maps также поддерживают заполнители `{quadkey}`, `{bbox-epsg-3857}` и `{subdomain}`. Для того чтобы мозаичный слой был частично прозрачным, используется значение непрозрачности 0,8. Обратите внимание, что непрозрачность и прозрачность, хотя и аналогично, используют Инвертированные значения. Для преобразования между ними просто вычтите значение из числа единиц.

> [!TIP]
> В Azure Maps слои можно легко отобразить под другими слоями, в том числе с базовыми слоями карт. Часто желательно отображать мозаичные слои под метками карты, чтобы их можно было легко читать. Метод `map.layers.add` принимает второй параметр, который представляет собой идентификатор слоя, в который следует вставить новый слой. Чтобы вставить мозаичный слой под метками карты, можно использовать следующий код: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure Maps мозаичного слоя](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Отображение данных дорожного движения

Данные трафика можно использовать как в Azure, так и в Google Maps.

**До: карты Google**

С помощью карт Google можно переносить данные потока трафика на карту, передавая значение true в метод `setTrafficEnabled` карты.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-traffic.png)</center> трафика Google Maps

**После: Azure Maps**

Azure Maps предоставляет несколько различных параметров для отображения трафика. Инциденты трафика, такие как замыкания на пути и всякое, могут отображаться в виде значков на карте. Поток трафика, Раздельная цветовая кодировка может быть наложен на карту, а цвета можно изменить, чтобы они были основаны относительно предельной скорости, относительно нормальной ожидаемой задержки или абсолютной задержки. Данные об инцидентах в Azure Maps обновляются каждую минуту и потоки данных каждые две минуты.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps трафика](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure Maps пакет SDK для Android.

> [!div class="nextstepaction"]
> [Использование элемента управления картой Android](how-to-use-android-map-control-library.md)
