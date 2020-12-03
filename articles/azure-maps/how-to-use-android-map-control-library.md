---
title: Приступая к работе с пакетом SDK для Android для Azure Maps
description: Познакомьтесь с Microsoft Azure картами пакет SDK для Android. См. статью создание проекта в Android Studio, установка пакета SDK и создание интерактивной схемы.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531267"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Приступая к работе с пакетом SDK для Android для Azure Maps

Пакет SDK для Android для Azure Maps — это библиотека векторных карт для Android. В этой статье описывается, как установить пакет SDK для Android для Azure Maps и загрузить карту.

## <a name="prerequisites"></a>Предварительные требования

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы Azure Maps

1. [Создайте учетную запись службы Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Получите первичный ключ подписки](quick-demo-map-app.md#get-the-primary-key-for-your-account), который иногда называется первичным ключом или ключом подписки.
Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).
3. [Скачайте и установите Android Studio Google](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Создание проекта в Android Studio

Выполните следующие действия, чтобы создать проект Android Studio.

1. Запустите Android Studio.
2. Щелкните **+ создать новый проект**.
3. На вкладке **Телефон и планшет** щелкните **пустое действие**. Нажмите кнопку **Далее**.
4. В разделе **Configure your project** (Настройка проекта) выберите `API 21: Android 5.0.0 (Lollipop)` в качестве минимального пакета SDK.
5. Выберите `Java` в качестве языка.
6. Примите значение по умолчанию `Name` для проекта. Нажмите кнопку **Готово**.

Дополнительные сведения об установке Android Studio и создании проекта см. в [документации по Android Studio](https://developer.android.com/studio/intro/).

![Создание проекта в Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Настройка устройства

Чтобы протестировать приложение во время разработки, вы можете использовать либо телефон Android, либо эмулятор Android.

Дополнительные сведения о настройке AVD (виртуального устройства Android) см. в [документации по Android Studio](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>Установка пакета SDK для Android для Azure Maps

Следующим шагом при создании приложения является установка пакета SDK для Android для Azure Maps.

Чтобы установить этот пакет SDK, выполните следующие действия.

1. На вкладке проект разверните узел **сценарии Gradle**. Откройте **сборку. gradle (проект: My_Application)** и добавьте следующий код в раздел **все проекты** `repositories`  :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Откройте **сборку. gradle (модуль: My_Application)**.

3. Убедитесь, что **minSdkVersion** в `defaultConfig` разделе находится в API 21 или более поздней версии.

4. Добавьте в раздел Android следующий код:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Добавьте в раздел `dependencies` следующий код:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. На главной панели инструментов щелкните **файл** , а затем выберите **синхронизировать проект с Gradle файлами**.

7. Откройте среду `res > layout > activity_main.xml`. Щелкните `Code` Просмотр в правом верхнем углу. Добавьте следующий XML-код в `<androidx.constraintlayout.widget.ConstraintLayout>` элемент.
    
    ```XML
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

8. В `java > com.example.myapplication > MainActivity.java` файле необходимо следующее:

    * Добавление импорта для пакета SDK для Azure Maps.
    * Задайте сведения для проверки подлинности Azure Maps.
    * Получите экземпляр элемента управления Map в методе **OnCreate** .

    Чтобы не добавлять данные проверки подлинности для каждого представления приложения, мы установим данные проверки подлинности глобально, вызывая `AzureMaps.setSubscriptionKey` . Можно также вызвать `AzureMaps.setAadProperties` , если вы хотите выполнить проверку подлинности с помощью Azure Active Directory.

    Элемент управления картой переопределяет следующие методы жизненного цикла класса MainActivity. Эти методы отвечают за управление жизненным циклом OpenGL Android.

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    Измените `MainActivity.java` файл следующим образом:

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

>[!WARNING]
>Возможно, Android Studio не импортировали необходимые классы.  В результате код будет содержать неразрешимые ссылки. Чтобы импортировать необходимые классы, просто наведите указатель мыши на каждую неразрешенную ссылку и нажмите `Alt + Enter` (Option + Return на компьютере Mac).

Android Studio выполнит сборку приложения за несколько секунд. После этого вы сможете протестировать приложение на эмулированном устройстве Android. Должна отобразиться карта примерно следующего вида.

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps в приложении Android":::

## <a name="localizing-the-map"></a>Локализация карты

Azure Maps пакет SDK для Android предоставляет три различных способа настройки языковых и региональных параметров для схемы.

1. Задайте языковые и региональные настройки, вызвав статические методы в классе Азуремапс.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Определение языковых и региональных параметров в XML-коде элемента управления Map.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Настройте язык и региональные параметры, вызвав методы в элементе управления картой. Этот параметр позволяет изменять параметры во время выполнения.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Ниже приведен пример Azure Maps с языком, установленным в `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Изображение карты Azure Maps с надписями на французском языке":::

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="navigating-the-map"></a>Перемещение по карте

Существует несколько способов, с помощью которых можно увеличить, сдвинуть, повернуть и наклонить карту. Ниже описаны различные способы перемещения по карте.

**Изменение масштаба карты**

- Коснитесь карты двумя пальцами и сведите их, чтобы уменьшить карту, или разведите пальцы, чтобы увеличить ее.
- Дважды коснитесь карты, чтобы увеличить ее масштаб на один уровень.
- Дважды коснитесь двумя пальцами, чтобы увеличить карту на один уровень.
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