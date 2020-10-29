---
title: Приступая к работе с элементом управления картой Android | Microsoft Azure Maps
description: Познакомьтесь с Azure Maps пакет SDK для Android. См. статью создание проекта в Android Studio, установка пакета SDK и создание интерактивной схемы.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 72bb821c0dfed6d3f9e7e2cc222242e65a35a011
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911058"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Приступая к работе с пакетом SDK для Android для Azure Maps

Пакет SDK для Android для Azure Maps — это библиотека векторных карт для Android. В этой статье описывается, как установить пакет SDK для Android для Azure Maps и загрузить карту.

## <a name="prerequisites"></a>Предварительные требования

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы Azure Maps

Чтобы выполнить процедуры, описанные в этой статье, сначала необходимо [создать учетную запись Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) в ценовой категории S1 и [получить первичный ключ](quick-demo-map-app.md#get-the-primary-key-for-your-account) для своей учетной записи.

Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Скачивание Android Studio

Прежде чем установить пакет SDK для Android для Azure Maps, скачайте Android Studio и создайте проект с пустым действием. Вы можете бесплатно скачать [Android Studio](https://developer.android.com/studio/) с сайта Google. 

## <a name="create-a-project-in-android-studio"></a>Создание проекта в Android Studio

Во-первых, создайте проект с пустым действием. Выполните следующие действия, чтобы создать проект Android Studio.

1. В разделе **Choose your project** (Выберите проект) выберите **Phone and Tablet** (Телефоны и планшеты). Приложение будет предназначено для этого форм-фактора.
2. На вкладке **Phone and Tablet** (Телефоны и планшеты) выберите **Empty Activity** (Пустое действие) и нажмите кнопку **Next** (Далее).
3. В разделе **Configure your project** (Настройка проекта) выберите `API 21: Android 5.0.0 (Lollipop)` в качестве минимального пакета SDK. Это самая ранняя версия, поддерживаемая пакетом SDK для Android для Azure Maps.
4. Примите значения по умолчанию `Activity Name` и `Layout Name` и нажмите кнопку **Finish** (Готово).

Дополнительные сведения об установке Android Studio и создании проекта см. в [документации по Android Studio](https://developer.android.com/studio/intro/).

![Создание проекта в Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Настройка виртуального устройства

Android Studio позволяет настроить виртуальное устройство Android на вашем компьютере. Это позволит тестировать приложение во время разработки. Чтобы настроить виртуальное устройство, щелкните значок диспетчера виртуальных устройств Android (AVD) в правом верхнем углу экрана проекта, а затем выберите **Create Virtual Device** (Создать виртуальное устройство). Вы можете также перейти к диспетчеру AVD, выбрав на панели инструментов **Tools** > **Android** > **AVD Manager** (Инструменты > Android > Диспетчер AVD). В категории **Phones** (Телефоны) выберите **Nexus 5X** и щелкните **Next** (Далее).

Дополнительные сведения о настройке AVD см. в [документации по Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Установка пакета SDK для Android для Azure Maps

Следующим шагом при создании приложения является установка пакета SDK для Android для Azure Maps. Чтобы установить этот пакет SDK, выполните следующие действия.

1. Откройте файл **build.gradle** верхнего уровня и добавьте следующий код во **все проекты** в разделе блока **репозиториев** .

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
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
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
    * получить экземпляр элемента управления картой в методе **onCreate** .

    Если задать сведения о проверке подлинности в классе `AzureMaps` глобально с помощью методов `setSubscriptionKey` или `setAadProperties`, не нужно будет добавлять эти сведения в каждое представление. 

    Элемент управления картой содержит собственные методы жизненного цикла для управления жизненным циклом OpenGL Android. Они должны вызываться непосредственно из содержащего их действия. Чтобы приложение правильно вызывало методы жизненного цикла элемента управления картой, переопределите приведенные ниже методы жизненного цикла в действии, содержащем элемент управления картой. Необходимо вызывать соответствующий метод элемента управления картой. 

    * onCreate(Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
    * onLowMemory() 

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

После выполнения предыдущих шагов вы, вероятно, получите предупреждения от Android Studio о коде. Чтобы устранить эти предупреждения, импортируйте классы, на которые ссылается `MainActivity.java`.

Вы можете автоматически импортировать эти классы, нажав клавиши ALT+ВВОД (OPTION+RETURN на компьютере Mac).

Нажмите кнопку выполнения, как показано на следующем рисунке (или нажмите клавиши CONTROL+R на компьютере Mac), чтобы выполнить сборку приложения.

![Нажмите кнопку "Выполнить"](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio выполнит сборку приложения за несколько секунд. После этого вы сможете протестировать приложение на эмулированном устройстве Android. Должна отобразиться карта примерно следующего вида.

<center>

![Azure Maps в приложении Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Локализация карты

Пакет SDK для Android для Azure Maps предоставляет три разных способа настройки языка и регионального представления для карты. В приведенном ниже коде показано, как установить французский язык (fr-FR) и региональное представление со значением "auto". 

Первый вариант — передать сведения о языке и региональном представлении в класс `AzureMaps`, используя статические методы `setLanguage` и `setView` глобально. Это позволит задать язык и региональное представление по умолчанию для всех элементов управления Azure Maps, загруженных в приложение.

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

Ниже приведен пример Azure Maps с установленным языком fr-FR и региональным представлением "auto".

<center>

![Изображение карты Azure Maps с надписями на французском языке](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="navigating-the-map"></a>Перемещение по карте

Существует несколько способов, с помощью которых можно увеличить, сдвинуть, повернуть и наклонить карту. Ниже описаны различные способы перемещения по карте.

**Изменение масштаба карты**

- Коснитесь карты двумя пальцами и сведите их, чтобы уменьшить карту, или разведите пальцы, чтобы увеличить ее.
- Дважды коснитесь карты, чтобы увеличить ее масштаб на один уровень.
- Дважды коснитесь карты двумя пальцами, чтобы уменьшить ее масштаб на один уровень.
- Коснитесь карты дважды, и при втором касании, удерживая палец на карте, проведите вверх, чтобы увеличить карту, либо проведите вниз, чтобы уменьшить ее.

**Сдвиг карты**

- Коснитесь карты и проведите в любом направлении.

**Поворот карты**

- Коснитесь карты двумя пальцами и вращайте ее.

**Наклон карты**

- Коснитесь карты двумя пальцами и проведите ими вверх или вниз.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как добавить данные наложения на карту:

> [!div class="nextstepaction"]
> [Добавление слоя символов на карту Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление фигур на карту Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Изменение стилей карт Android](./set-android-map-styles.md)