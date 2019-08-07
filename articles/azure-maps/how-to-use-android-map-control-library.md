---
title: Начало работы с элементом управления картой Android в Azure Maps | Документация Майкрософт
description: Элемент управления картой Android в Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a3423635ab226693e0b3b057e2c2cb441861ea1b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839406"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Приступая к работе с Azure Maps пакет SDK для Android

Azure Maps пакет SDK для Android — это библиотека векторных карт для Android. В этой статье описывается процесс установки Azure Maps пакет SDK для Android и загрузки карт.

## <a name="prerequisites"></a>Предварительные требования

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы "Карты Azure";

Для выполнения процедур, описанных в этой статье, необходимо сначала [создать учетную запись Azure Maps](how-to-manage-account-keys.md) в ценовой категории S1.

### <a name="download-android-studio"></a>Скачать Android Studio

Скачайте Android Studio и создайте проект с пустым действием, прежде чем устанавливать пакет SDK для Android Azure Maps. Вы можете [скачать Android Studio](https://developer.android.com/studio/) бесплатно из Google. 

## <a name="create-a-project-in-android-studio"></a>Создание проекта в Android Studio

Сначала создайте новый проект с пустым действием. Выполните следующие действия, чтобы создать проект Android Studio.

1. В разделе **Выбор проекта**выберите **Телефон и планшет**. Приложение будет выполняться на этом конструктивном коэффициенте.
2. На вкладке **Телефон и планшет** выберите пустое **действие**, а затем нажмите кнопку **Далее**.
3. В разделе **Configure your project** (Настройка проекта) выберите `API 21: Android 5.0.0 (Lollipop)` в качестве минимального пакета SDK. Это самая ранняя версия, поддерживаемая пакет SDK для Androidом Azure Maps.
4. Примите значения по `Activity Name` умолчанию и `Layout Name` нажмите кнопку **Готово**.

Дополнительные сведения об установке Android Studio и создании нового проекта см. в [документации по Android Studio](https://developer.android.com/studio/intro/) .

![Создайте проект](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Настройка виртуального устройства

Android Studio позволяет настроить виртуальное устройство Android на вашем компьютере. Это позволит протестировать приложение во время разработки. Чтобы настроить виртуальное устройство, щелкните значок диспетчера виртуальных устройств Android (AVD) в правом верхнем углу экрана проекта, а затем выберите **создать виртуальное устройство**. Вы также можете перейти к AVD Manager, выбрав **инструменты** > **Android** > **AVD Manager** на панели инструментов. В категории **телефоны** выберите **5x хранилища**, а затем нажмите кнопку **Далее**.

Дополнительные сведения о настройке AVD см. в [документации по Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Установка Azure Maps пакет SDK для Android

Следующим шагом при создании приложения является установка пакет SDK для Android Azure Maps. Выполните следующие действия, чтобы установить пакет SDK.

1. Откройте файл **Build. gradle** верхнего уровня и добавьте следующий код в раздел **все проекты**, репозитории:

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
    > Пакет SDK для Android Azure Maps регулярно обновляется и улучшается. Чтобы получить последнюю версию Azure Mapsной реализации, можно просмотреть документацию по [управлению картой Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) . Кроме того, можно задать номер версии от "0,2" до "0 +", чтобы он всегда указывал на последнюю версию.

3. Измените > макетRES > **activity_main. XML** и замените его на код:
    
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

    Настройка сведений о проверке подлинности в классе Азуремапс глобально с помощью методов Сетсубскриптионкэй или Сетаадпропертиес делает его невозможным, поэтому вам не придется добавлять данные проверки подлинности в каждое представление. Элемент управления картой содержит собственные методы жизненного цикла для управления жизненным циклом OpenGL Android, который должен вызываться непосредственно из содержащего действия. Чтобы приложение было правильно, вызовите методы жизненного цикла элемента управления Map, переопределите следующие методы жизненного цикла в действии, содержащем элемент управления картой, и вызовите соответствующий метод управления картой. 

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

После выполнения предыдущих шагов вы, вероятно, получите предупреждения от Android Studio о коде. Чтобы устранить эти предупреждения, импортируйте классы, на `MainActivity.java`которые имеются ссылки в.

Вы можете автоматически импортировать эти классы, нажав сочетание клавиш ALT + ВВОД (параметр + вернуть на компьютере Mac).

Нажмите кнопку выполнить, как показано на следующем рисунке (или нажмите клавиши Control + R на компьютере Mac), чтобы создать приложение.

![Нажмите кнопку "Выполнить"](./media/how-to-use-android-map-control-library/run-app.png)

Создание приложения Android Studio займет несколько секунд. После завершения сборки можно протестировать приложение на устройстве с эмуляцией Android. Вы должны увидеть такую карту:

<center>

![Схема Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Локализация схемы

Azure Maps пакет SDK для Android предоставляет три различных способа настройки языка и регионального представления для отображения. В следующем коде показано, как задать для языка значение французский (fr-FR), а для региона — значение Auto. 

Первый вариант заключается в том, чтобы передавать язык и просматривать региональные сведения в `AzureMaps` класс, используя глобально `setView` статические `setLanguage` методы и. Это позволит задать язык по умолчанию и региональные представления для всех элементов управления Azure Maps, загруженных в приложение.

```Java
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

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Третий вариант заключается в программном задании языка и регионального представления карты с помощью метода Maps `setStyle` . Это можно сделать в любое время, чтобы изменить язык и региональные представления на карте.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Ниже приведен пример Azure Maps с языком, для которого задано значение "fr-FR", а для регионального представления задано значение "Авто".

<center>

![Изображение схемы, показывающее метки на французском языке](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="next-steps"></a>Следующие шаги

Узнайте, как добавить данные наложения на карту.

> [!div class="nextstepaction"]
> [Добавление слоя символов на карту Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Добавление фигур на карту Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Изменение стилей карты в картах Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
