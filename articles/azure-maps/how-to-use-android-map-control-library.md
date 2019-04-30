---
title: Как использовать элемент управления Android map в "карты Azure" | Документация Майкрософт
description: Элемент управления Android карты в "карты Azure".
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770371"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Как использовать пакет SDK Android карт Azure

Azure Maps Android SDK — это библиотека карты вектор для Android. В этой статье поможет выполнить процесс установки Azure Maps Android SDK, загрузка карту и размещение ПИН-кода на карте.

## <a name="prerequisites"></a>Технические условия

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы "Карты Azure";

Чтобы выполнить процедуры, описанные в этой статье, необходимо сначала [создать учетную запись Azure Maps](how-to-manage-account-keys.md) в ценовой категории S1.

### <a name="download-android-studio"></a>Скачать Android Studio

Необходимо скачать Android Studio и создайте проект с пустым действием, перед установкой Azure Maps пакета SDK для Android. Вы можете [скачать Android Studio](https://developer.android.com/studio/) бесплатно от Google. 

## <a name="create-a-project-in-android-studio"></a>Создание проекта в Android Studio

Во-первых необходимо создать новый проект с пустым действием. Выполните следующие действия, чтобы создать проект Android Studio.

1. В разделе **выберите свой проект**выберите **телефонов и планшетов**. Приложение будет выполняться на этом форм-факторе.
2. На **телефонов и планшетов** выберите **пустое действие**, а затем выберите **Далее**.
3. В разделе **Configure your project** (Настройка проекта) выберите `API 21: Android 5.0.0 (Lollipop)` в качестве минимального пакета SDK. Это самая ранняя версия, поддерживаемая Azure Maps пакета SDK для Android.
4. Примите имя по умолчанию `Activity Name` и `Layout Name` и выберите **Готово**.

См. в разделе [документации Android Studio](https://developer.android.com/studio/intro/) дополнительную помощь с установки Android Studio и создания нового проекта.

![Создание проекта](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Настройка виртуального устройства

Android Studio позволяет настроить виртуальное устройство Android на вашем компьютере. Это поможет вам протестировать приложения во время разработки. Чтобы настроить виртуальное устройство, щелкните значок диспетчера виртуальных устройств Android (AVD) в правом верхнем углу экрана проекта и выберите **Создание виртуального устройства**. Также можно получить в диспетчере AVD, выбрав **средства** > **Android** > **диспетчер AVD** на панели инструментов. В **телефоны** категорию **Nexus 5 X**, а затем выберите **Далее**.

Дополнительные сведения о настройке в виртуальное устройство AVD [документации Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Установите пакет SDK для Android Azure Maps

Следующий шаг в создании приложения является установки Azure Maps Android SDK. Выполните следующие действия, чтобы установить пакет SDK.

1. Добавьте следующий код, чтобы **все проекты**, **репозиториев** блока в вашей **build.gradle** файла.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Обновление вашей **app/build.gradle** и добавьте в него следующий код:

    1. Добавьте следующий код в блок Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Обновите свой блок зависимостей и добавьте в него следующий код:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Настройка разрешений, добавив следующий код XML для вашей **AndroidManifest.xml** файла:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Изменить **res** > **макета** > **activity_main.xml** чтобы он выглядел этот XML-код:
    
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

5. Отредактируйте **MainActivity.java**, чтобы создать класс действий представления карты. После изменения его, он должен выглядеть как этот класс:

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

## <a name="import-classes"></a>Импортировать классы

После завершения предыдущих шагов, может появиться предупреждения из Android Studio о часть кода. Чтобы устранить эти предупреждения, импортировать классы, на которые ссылается `MainActivity.java`.

Вы можете автоматически импортировать эти классы, выбрав Alt + Ввод (параметр + результат на компьютере Mac).

Выберите кнопку выполнения, как показано на следующем рисунка (или сочетание управления + R на компьютере Mac), для построения приложения.

![Нажмите кнопку "Выполнить"](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio займет несколько секунд для построения приложения. После завершения сборки необходимо протестировать приложение в Android эмулированного устройства. Вы должны увидеть карту следующего вида:

![Карта Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Добавление метки на карту

Чтобы добавить маркер в сопоставление, добавьте `mapView.getMapAsync()` функция `MainActivity.java`. Последний `MainActivity.java` код должен выглядеть следующим образом:

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

Повторный запуск приложения. Вы должны увидеть маркер на карте, как показано ниже:

![Метка на карте Android](./media/how-to-use-android-map-control-library/android-map-pin.png)