---
title: Начало работы с управлением картами Android Карты Microsoft Azure
description: В этой статье вы узнаете, как начать работу с управлением картой Android с помощью Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548538"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Начало работы с Azure Maps Android SDK

Azure Maps Android SDK — это библиотека векторных карт для Android. Эта статья проведет вас через процессы установки Azure Maps Android SDK и загрузки карты.

## <a name="prerequisites"></a>предварительные требования

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы Azure Maps

Для завершения процедур в этой статье сначала необходимо [создать учетную запись Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) на уровне ценообразования S1 и [получить основной ключ](quick-demo-map-app.md#get-the-primary-key-for-your-account) для своей учетной записи.

Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Скачать Android Studio

Скачать Android Studio и создать проект с пустой деятельности, прежде чем установить Azure Maps Android SDK. Вы можете [скачать Android Studio](https://developer.android.com/studio/) бесплатно от Google. 

## <a name="create-a-project-in-android-studio"></a>Создание проекта в Android Studio

Во-первых, создать новый проект с пустым действием. Выполнить эти шаги для создания проекта Android Studio:

1. Под **вами Выберите свой проект,** выберите **телефон и планшет.** Ваше приложение будет работать на этом форм-факторе.
2. На вкладке **«Телефон и планшет»** выберите **«Пустое действие»,** а затем выберите **далее.**
3. В разделе **Configure your project** (Настройка проекта) выберите `API 21: Android 5.0.0 (Lollipop)` в качестве минимального пакета SDK. Это самая ранняя версия, поддерживаемая Android SDK Azure Maps.
4. Примите `Activity Name` значение `Layout Name` по умолчанию и выберите **Finish.**

Смотрите [документацию Android Studio](https://developer.android.com/studio/intro/) для получения дополнительной помощи с установкой Android Studio и созданиенового проекта.

![Создание проекта в студии Android ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Настройка виртуального устройства

Android Studio позволяет настроить виртуальное устройство Android на вашем компьютере. Это может помочь вам протестировать приложение во время разработки. Чтобы настроить виртуальное устройство, выберите значок менеджера виртуального устройства Android (AVD) в правом верхнем углу экрана проекта, а затем выберите **Создать виртуальное устройство.** Вы также можете добраться до МЕНЕДЖЕРА AVD, выбрав **Инструменты** > **Android** > **AVD Manager** из панели инструментов. В категории **Телефоны** выберите **Nexus 5X,** а затем выберите **Next.**

Вы можете узнать больше о настройке AVD в [документации Android Studio.](https://developer.android.com/studio/run/managing-avds)

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Установка Azure Maps Android SDK

Следующим шагом в создании приложения является установка Azure Maps Android SDK. Выполнить следующие шаги для установки SDK:

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
    * получить экземпляр управления картой в методе **onCreate**

    Если задать сведения о проверке подлинности в классе `AzureMaps` глобально с помощью методов `setSubscriptionKey` или `setAadProperties`, не нужно будет добавлять эти сведения в каждое представление. 

    Элемент управления картой содержит собственные методы жизненного цикла для управления жизненным циклом OpenGL Android. Эти методы жизненного цикла должны быть вызваны непосредственно из содержащего действия. Для правильного вызова методов жизненного цикла управления картой необходимо переопределить следующие методы жизненного цикла в действии, содержащем элемент управления картой. И вы должны вызвать соответствующий метод управления картой. 

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

## <a name="import-classes"></a>Классы импорта

После завершения предыдущих шагов, вы, вероятно, получите предупреждения от Android Studio о некоторых кодах. Чтобы разрешить эти предупреждения, импортируйте `MainActivity.java`классы, на которые ссылаются в .

Вы можете автоматически импортировать эти классы, выбрав Alt-Enter (Option-Return on a Mac).

Для создания приложения выберите кнопку run, показанную на следующем графике (или нажмите на Control-R на Mac).

![Нажмите кнопку "Выполнить"](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio займет несколько секунд, чтобы построить приложение. После завершения сборки вы можете протестировать приложение в эмулированном устройстве Android. Вы должны увидеть карту, как эта:

<center>

![Карты Azure в приложении для Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Локализация карты

Azure Maps Android SDK предоставляет три различных способа настройки языка и регионального представления карты. В следующем коде показано, как установить язык на французский ("fr-FR") и региональный вид на "авто". 

Первый вариант заключается в том, чтобы `AzureMaps` передать язык `setLanguage` `setView` и просматривать региональную информацию в класс, используя статические и методы во всем мире. Это установит язык по умолчанию и региональный представление во всех элементах управления Azure Maps, загруженных в приложение.

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

Вот пример Azure Maps с формулировкой "fr-FR" и региональным представлением, установленным на "авто".

<center>

![Карты Azure, изображение карты, показывающее этикетки на французском языке](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="navigating-the-map"></a>Навигация по карте

Существует несколько различных способов масштабирования, панорамирования, вращения и разобрана карты. Ниже описаны все различные способы навигации по карте.

**Увеличить карту**

- Коснитесь карты двумя пальцами и щепотку вместе, чтобы увеличить или распространить пальцы друг от друга, чтобы увеличить.
- Дважды коснитесь карты, чтобы увеличить на один уровень.
- Двойной кран двумя пальцами, чтобы увеличить карту на один уровень.
- Нажмите дважды; на втором нажатии, держите палец на карте и перетащите вверх, чтобы увеличить, или вниз, чтобы увеличить.

**Панорамирование карты**

- Коснитесь карты и перетащите в любом направлении.

**Поверните карту**

- Коснитесь карты двумя пальцами и поверните.

**Шаг карту**

- Коснитесь карты двумя пальцами и перетащите их вверх или вниз вместе.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как добавить данные о наложении на карту:

> [!div class="nextstepaction"]
> [Добавление слоя символов на карту Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление фигур на карте Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Изменение стилей карт на картах Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
