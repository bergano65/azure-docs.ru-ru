---
title: Как использовать элемент управления картой в Android в службе Azure Maps | Документация Майкрософт
description: Использование элемента управления картой в Android в Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 57cc585d621c71872a4b7658c74f581c8998b245
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341085"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Использование пакета SDK для Android в Azure Maps

Пакет SDK для Android в Azure Maps — это библиотека векторных карт для Android. В этой статье вы узнаете, как установить пакет SDK для Android в Azure Maps, загрузить карту и разместить метку на ней.

## <a name="prerequisites-to-get-started"></a>Предварительные требования для начала работы

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы "Карты Azure"; 

Для выполнения заданий из этого руководства сначала необходимо ознакомиться со статьей [Управление учетной записью и ключами службы Azure Maps](how-to-manage-account-keys.md), чтобы создать подписку учетной записи ценовой категории S1 и управлять ею.

### <a name="download-android-studio"></a>Скачивание Android Studio

Вы можете бесплатно скачать [Android Studio](https://developer.android.com/studio/) от Google. Чтобы установить пакет SDK для Android в Azure Maps, необходимо сначала скачать Android Studio и создать проект с пустым действием.

## <a name="create-a-project-in-android-studio"></a>Создание проекта в Android Studio

Вам нужно будет создать проект с пустым действием. Чтобы создать проект Android Studio, выполните следующие действия.

1. В разделе *Choose your project* (Выбор проекта) в качестве форм-фактора выберите Phone and Tablet (Телефоны и планшеты), на котором будет работать приложение.
2. Под форм-фактором выберите *Empty Activity* (Пустое действие) и щелкните **Далее**.
3. В разделе *Configure your project* (Настройка проекта) выберите `API 21: Android 5.0.0 (Lollipop)` в качестве минимального пакета SDK. Это самая ранняя версия, поддерживаемая пакетом SDK для Android в Azure Maps.
4. Примите значение по умолчанию `Activity Name` и `Layout Name` и нажмите кнопку **Готово**.

Дополнительные сведения об установке Android Studio и создании проекта см. в [документации по Android Studio](https://developer.android.com/studio/intro/).

![создание проекта](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Настройка виртуального устройства

Android Studio позволяет настроить виртуальное устройство Android на вашем компьютере. Это может помочь при тестировании приложения во время разработки. Чтобы настроить виртуальное устройство, щелкните значок диспетчера виртуальных устройств Android (AVD) в правом верхнем углу экрана проекта. Затем нажмите кнопку **Create Virtual Device** (Создать виртуальное устройство). Вы также можете перейти к диспетчеру с помощью панели инструментов, выбрав **Tools > Android > AVD Manager** (Инструменты > Android > Диспетчер AVD). В категории **Phones** (Телефоны) выберите **Nexus 5X** и щелкните **Next** (Далее).

Дополнительные сведения о настройке AVD см. в [документации по Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Установка пакета SDK для Android в Azure Maps

Прежде чем перейти к созданию приложения, выполните следующие действия, чтобы установить пакет SDK для Android в Azure Maps. 

1. Добавьте следующий код **всем проектам** в блоке репозитория в файле **build.gradle**.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Обновите **app/build.gradle** и добавьте в него следующее.

    1. Добавьте следующее в блок Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Обновите блок зависимостей и добавьте следующее:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Настройте разрешения, добавив следующее к **AndroidManifest.xml**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Отредактируйте **res > layout > activity_main.xml**, чтобы он выглядел как следующий XML:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Отредактируйте **MainActivity.java**, чтобы создать класс действий представления карты. После редактирования он должен выглядеть следующим образом:

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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

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

## <a name="import-classes"></a>Импорт классов

Выполнив описанные выше шаги, вы, скорее всего, получите предупреждения от Android Studio о части текста в коде. Чтобы устранить эти предупреждения, импортируйте классы, на которые ссылается `MainActivity.java`.

Вы можете автоматически импортировать эти классы, нажав сочетание клавиш `Alt`+`Enter` (`Option`+`Return` на Mac). 

Нажмите кнопку **запуска приложения** (или `Control`+`R` на Mac), чтобы создать приложение.

![Нажмите кнопку "Выполнить"](./media/how-to-use-android-map-control-library/run-app.png)

Создание приложения для Android Studio займет несколько секунд. После этого вы можете протестировать приложение на эмулированном устройстве Android. Вы увидите карту, подобную приведенной ниже.

![Карта Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Добавление метки на карту

Чтобы добавить метку на карту, добавьте функцию `mapView.getMapAsync()` в `MainActivity.java`. Итоговый `MainActivity.java` должен выглядеть примерно так:

```java
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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
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

При повторном запуске приложения вы увидите метку на карте, как показано ниже.

![Метка на карте Android](./media/how-to-use-android-map-control-library/android-map-pin.png)