---
title: Начало работы с элементом управления картой Android | Карты Microsoft Azure
description: В этой статье вы узнаете, как начать работу с элементом управления карты Android с помощью Microsoft Azure карт пакет SDK для Android.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548538"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Приступая к работе с Azure Maps пакет SDK для Android

Azure Maps пакет SDK для Android — это библиотека векторных карт для Android. В этой статье описывается процесс установки Azure Maps пакет SDK для Android и загрузки карт.

## <a name="prerequisites"></a>предварительные требования

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы Azure Maps

Чтобы выполнить процедуры, описанные в этой статье, сначала необходимо [создать учетную запись Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) в ценовой категории S1 и [получить первичный ключ](quick-demo-map-app.md#get-the-primary-key-for-your-account) для вашей учетной записи.

Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Скачать Android Studio

Скачайте Android Studio и создайте проект с пустым действием, прежде чем устанавливать пакет SDK для Android Azure Maps. Вы можете [скачать Android Studio](https://developer.android.com/studio/) бесплатно из Google. 

## <a name="create-a-project-in-android-studio"></a>Создание проекта в Android Studio

Сначала создайте новый проект с пустым действием. Выполните следующие действия, чтобы создать проект Android Studio.

1. В разделе **Выбор проекта**выберите **Телефон и планшет**. Приложение будет выполняться на этом конструктивном коэффициенте.
2. На вкладке **Телефон и планшет** выберите **пустое действие**, а затем нажмите кнопку **Далее**.
3. В разделе **Configure your project** (Настройка проекта) выберите `API 21: Android 5.0.0 (Lollipop)` в качестве минимального пакета SDK. Это самая ранняя версия, поддерживаемая пакет SDK для Androidом Azure Maps.
4. Примите значения по `Activity Name` умолчанию и `Layout Name` нажмите кнопку **Готово**.

Дополнительные сведения об установке Android Studio и создании нового проекта см. в [документации по Android Studio](https://developer.android.com/studio/intro/) .

![Создание проекта в Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Настройка виртуального устройства

Android Studio позволяет настроить виртуальное устройство Android на вашем компьютере. Это позволит протестировать приложение во время разработки. Чтобы настроить виртуальное устройство, щелкните значок диспетчера виртуальных устройств Android (AVD) в правом верхнем углу экрана проекта, а затем выберите **создать виртуальное устройство**. Вы также можете перейти к AVD Manager, выбрав **инструменты** > **Android** > **AVD Manager** на панели инструментов. В категории **телефоны** выберите **5x хранилища**, а затем нажмите кнопку **Далее**.

Дополнительные сведения о настройке AVD см. в [документации по Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Установка Azure Maps пакет SDK для Android

Следующим шагом при создании приложения является установка пакет SDK для Android Azure Maps. Выполните следующие действия, чтобы установить пакет SDK.

1. Откройте файл **build.gradle** верхнего уровня и добавьте следующий код во **все проекты** в разделе блока **репозиториев**.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Обновите файл **app/build.gradle** и добавьте в него следующий код.
    
    1. Убедитесь, что **minSdkVersion** проекта находится на уровне версии API 21 или более поздней.

    2. Добавьте в раздел Android следующий код:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Обновите блок зависимостей и добавьте новую строку зависимости реализации для последнего пакета SDK для Android в Azure Maps:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Перейдите в раздел **Файл** на панели инструментов, а затем щелкните **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle).
3. Добавьте фрагмент карты в основное действие (res \> layout \> activity\_main.xml):
    
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

4. В файле **MainActivity.java** нужно сделать следующее:
    
    * добавить операции импорта для пакета SDK в Azure Maps;
    * настроить сведения о проверке подлинности Azure Maps;
    * получение экземпляра элемента управления Map в методе **OnCreate**

    Если задать сведения о проверке подлинности в классе `AzureMaps` глобально с помощью методов `setSubscriptionKey` или `setAadProperties`, не нужно будет добавлять эти сведения в каждое представление. 

    Элемент управления картой содержит собственные методы жизненного цикла для управления жизненным циклом OpenGL Android. Эти методы жизненного цикла должны вызываться непосредственно из содержащего их действия. Чтобы приложение правильно вызывало методы жизненного цикла элемента управления картой, необходимо переопределить следующие методы жизненного цикла в действии, которое содержит элемент управления Map. И необходимо вызвать соответствующий метод управления картой. 

    * OnCreate (пакет) 
    * OnStart () 
    * onresume () 
    * OnPause () 
    * OnStop () 
    * OnDestroy () 
    * Онсавеинстанцестате (пакет) 
    * Онловмемори () 

    Измените файл **MainActivity.java** следующим образом:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

## <a name="import-classes"></a>Импорт классов

После выполнения предыдущих шагов вы, вероятно, получите предупреждения от Android Studio о коде. Чтобы устранить эти предупреждения, импортируйте классы, на `MainActivity.java`которые имеются ссылки в.

Вы можете автоматически импортировать эти классы, нажав сочетание клавиш ALT + ВВОД (параметр + вернуть на компьютере Mac).

Нажмите кнопку выполнить, как показано на следующем рисунке (или нажмите клавиши Control + R на компьютере Mac), чтобы создать приложение.

![Нажмите кнопку "Выполнить"](./media/how-to-use-android-map-control-library/run-app.png)

Создание приложения Android Studio займет несколько секунд. После завершения сборки можно протестировать приложение на устройстве с эмуляцией Android. Вы должны увидеть такую карту:

<center>

![Azure Maps в приложении Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Локализация карты

Azure Maps пакет SDK для Android предоставляет три различных способа настройки языка и регионального представления для отображения. В следующем коде показано, как задать для языка значение французский (fr-FR), а для региона — значение Auto. 

Первый вариант заключается в том, чтобы передавать язык и просматривать региональные сведения в `AzureMaps` класс, используя `setLanguage` глобально `setView` статические методы и. Это позволит задать язык по умолчанию и региональные представления для всех элементов управления Azure Maps, загруженных в приложение.

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

Второй вариант — передать сведения о языке и представлении в XML-коде элемента управления карты.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Третий вариант — задать язык и региональное представление карты программным образом с помощью метода `setStyle` карт. Это можно сделать в любое время, чтобы изменить язык и региональное представление для карты.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Ниже приведен пример Azure Maps с языком, для которого задано значение "fr-FR", а для регионального представления задано значение "Авто".

<center>

![Azure Maps, изображение на карте, показывающее метки на французском языке](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="navigating-the-map"></a>Навигация по карте

Существует несколько различных способов, с помощью которых можно увеличить, сдвигаемых, повернуть и наклонить карту. Ниже приведены все различные способы перемещения по карте.

**Изменить масштаб схемы**

- Нажимайте карту с двумя пальцами и сжимается вместе, чтобы уменьшить или распределить пальцы, чтобы увеличить масштаб.
- Дважды коснитесь элемента Map, чтобы увеличить масштаб на один уровень.
- Дважды коснитесь двумя пальцами, чтобы развернуть карту на один уровень.
- Коснитесь дважды; во втором касании Держите палец на карте и перетащите указатель вверх, чтобы увеличить или уменьшить масштаб.

**Панорамирование схемы**

- Наведите указатель мыши на карту и перетащите ее в любом направлении.

**Поворот схемы**

- Нажимайте карту двумя пальцами и вращайте.

**Шаг на карте**

- Переместите карту двумя пальцами и перетащите их вверх или вниз вместе.

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте, как добавить данные наложения на карту.

> [!div class="nextstepaction"]
> [Добавление слоя символов на карту Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление фигур на карту Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Изменение стилей карты в картах Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
