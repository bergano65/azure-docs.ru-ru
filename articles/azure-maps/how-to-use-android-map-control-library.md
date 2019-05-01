---
title: Приступая к работе с элементом управления Android карты в "карты Azure" | Документация Майкрософт
description: Элемент управления Android карты в "карты Azure".
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e655b442ba9290d4b4525108521f2d1a0c766b48
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869822"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Приступая к работе с Azure Maps пакета SDK для Android

Azure Maps Android SDK — это библиотека карты вектор для Android. В этой статье поможет выполнить процесс установки Android SDK для карты и загрузке карту.

## <a name="prerequisites"></a>Технические условия

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы "Карты Azure";

Чтобы выполнить процедуры, описанные в этой статье, необходимо сначала [создать учетную запись Azure Maps](how-to-manage-account-keys.md) в ценовой категории S1.

### <a name="download-android-studio"></a>Скачать Android Studio

Необходимо скачать Android Studio и создайте проект с пустым действием, прежде чем устанавливать пакет Azure Maps Android SDK. Вы можете [скачать Android Studio](https://developer.android.com/studio/) бесплатно от Google. 

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

1. Откройте верхнего уровня **build.gradle** файл и добавьте следующий код, чтобы **все проекты**, **репозиториев** блокировать раздел:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Обновление вашей **app/build.gradle** и добавьте в него следующий код:
    
    1. Убедитесь, что ваш проект **minSdkVersion** API 21 или более поздней версии.

    2. Добавьте следующий код в раздел Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Обновите свой блок зависимостей и добавьте новую строку реализации зависимостей для последней Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Azure Maps пакета SDK для Android выполняется регулярно обновлены и улучшены. Вы увидите [Приступая к работе с элементом управления Android карты](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) документации, чтобы получить номер последней версии реализации службы карт Azure. Кроме того можно задать номер версии из «0,2» на «0 +», чтобы он всегда указывать до последней версии.

3. Изменить **res** > **макета** > **activity_main.xml** и замените его следующим:
    
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

4. В **MainActivity.java** файл, вам потребуется:
    
    * добавить оператор imports для пакета SDK Azure Maps
    * набор сведений для проверки подлинности службы карт Azure
    * получить экземпляр элемента управления map в **onCreate** метод

    Сведения о проверке подлинности в классе AzureMaps глобально с помощью методов setSubscriptionKey или setAadProperties становится его, вам не придется добавить сведений для проверки подлинности для каждого представления. Элемент управления map содержит собственные методы жизненного цикла для управления жизненным циклом Android OpenGL, который должен вызываться непосредственно из содержащего их действия. Чтобы приложение правильно, вызывать методы жизненного цикла элемента управления картой необходимо переопределите следующие методы жизненного цикла в действии, которое содержит элемент управления картой и вызвать метод управления соответствующей карты. 

    Изменить **MainActivity.java** файл следующим образом:
    
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

<center>

![Android карты](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Дальнейшие действия

Чтобы добавить содержимое на карту, см. в разделе:

> [!div class="nextstepaction"]
> [Добавьте символ слой в карту Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Добавление фигур в Android карту](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Изменение стилей карт в Android maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)


