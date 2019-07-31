---
title: Учебник. Пошаговые инструкции по созданию нового приложения Android с использованием Пространственных привязок Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как создать новое приложение Android с использованием Пространственных привязок Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 499b08dbdc8e798a884b721bcba51be1f6973df6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562387"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Руководство по Пошаговые инструкции по созданию нового приложения Android с использованием Пространственных привязок Azure

В этом учебнике описывается, как создать новое приложение Android, в котором функции ARCore интегрируются с Пространственными привязками Azure.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются:

- Компьютер под управлением macOS или Windows с <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 или более поздних версий</a>.
- Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.

## <a name="getting-started"></a>Начало работы

Запустите Android Studio. В окне **приветствия Android Studio** щелкните **Start a new Android Studio project** (Создать проект Android Studio). Если у вас уже есть открытый проект, выберите **File (Файл)** ->**New Project (Создать проект)** .

В окне **создания нового проекта** в разделе **Phone and Tablet** (Телефоны и планшеты) выберите **Empty Activity** (Пустое действие) и щелкните **Далее**. Затем для параметра **Minimum API level** (Минимальный уровень API) выберите значение `API 26: Android 8.0 (Oreo)` и убедитесь, что для параметра **Language** (Язык) указано значение `Java`. Вы можете изменить имя и расположение проекта или имя пакета. Сохраните значения остальных параметров неизменными. Нажмите кнопку **Готово** Запустится **установщик компонентов**. Когда он завершит свою работу, щелкните **Готово**. После некоторой подготовки Android Studio откроет интегрированную среду разработки.

## <a name="trying-it-out"></a>Проверка работы

Чтобы протестировать новое приложение, подключите устройство с поддержкой функций разработки к локальному компьютеру разработки с помощью кабеля USB. Щелкните **Run** (Запуск) ->**Run 'app'** (Запустить приложение). В окне **Select Deployment Target** (Выбор цели развертывания) выберите подключенное устройство и щелкните **ОК**. Android Studio установит приложение на подключенном устройстве и запустит его. Вы увидите сообщение "Hello World!" в окне приложения, которое откроется на устройстве. Щелкните **Run** (Запуск) ->**Stop 'app'** (Остановить приложение).

## <a name="integrating-arcore"></a>Интеграция _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> — это платформа Google для создания функций дополненной реальности, которая позволяет устройству отслеживать положение в пространстве и генерировать собственное представление о реальном мире.

Измените файл `app\manifests\AndroidManifest.xml`, включив следующие записи в корневой узел `<manifest>`. Этот фрагмент кода выполняет несколько задач:

- предоставляет приложению доступ к камере устройства;
- настраивает видимость приложения в Google Play Маркет, чтобы оно отображалось только для устройств с поддержкой ARCore;
- настраивает скачивание и установку ARCore при установке приложения из Google Play Маркет, если этот компонент еще не установлен.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Измените `Gradle Scripts\build.gradle (Module: app)`, добавив указанный ниже фрагмент. Этот код обеспечит использование приложением ARCore версии 1.8. Если после этого изменения вы получите уведомление из Gradle с предложением выполнить синхронизацию, щелкните **Sync now** (Синхронизировать сейчас).

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.8.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Интеграция _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> упрощает отрисовку реалистичных объемных сцен в приложениях дополненной реальности, не требуя при этом изучать OpenGL.

Измените `Gradle Scripts\build.gradle (Module: app)`, добавив указанный ниже фрагмент. Этот код позволяет приложению использовать языковые конструкции из Java 8, что необходимо для `Sceneform`. Также он обеспечит использование приложением `Sceneform` версии 1.8 в соответствии с используемой версией ARCore. Если после этого изменения вы получите уведомление из Gradle с предложением выполнить синхронизацию, щелкните **Sync now** (Синхронизировать сейчас).

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.8.0'
    ...
}
```

Откройте `app\res\layout\activity_main.xml` и замените существующий элемент "Hello World" `<TextView>` указанным ниже кодом ArFragment. Этот код отображает на экране видео с камеры, что позволяет ARCore отслеживать местоположение устройства при его перемещении.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Повторно разверните](#trying-it-out) приложение на устройстве, чтобы проверить его еще раз. На этот раз вы получите запрос на предоставление разрешений для камеры. Подтвердив этот запрос, вы увидите на экране видео с камеры.

## <a name="place-an-object-in-the-real-world"></a>Размещение объекта в реальном мире

Давайте создадим новый объект и разместим его с помощью приложения. Сначала добавьте следующие операторы import в файл `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Затем добавьте следующие переменные-члены в класс `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Теперь добавьте указанный ниже код в метод `app\java\<PackageName>\MainActivity` `onCreate()`. Этот код подключает прослушиватель с именем `handleTap()`, который отслеживает касание экрана пользователем устройства. Если это касание происходит на поверхности из реального мира, которая распознана функцией отслеживания в ARCore, выполняется код прослушивателя.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Наконец, добавьте указанный ниже метод `handleTap()`, который выполняет компоновку. Этот код создает сферу и помещает ее в том расположении, где произошло касание. Изначально эта сфера будет черной, так как параметр `this.recommendedSessionProgress` имеет значение 0. Мы скорректируем это значение позднее.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Повторно разверните](#trying-it-out) приложение на устройстве, чтобы проверить его еще раз. Теперь измените положение устройства в пространстве, чтобы запустить распознавание окружающей среды с помощью ARCore. Затем коснитесь экрана, чтобы создать черную сферу и поместить ее на выбранную поверхность.

## <a name="attach-a-local-azure-spatial-anchor"></a>Присоединение локальной пространственной привязки Azure

Измените `Gradle Scripts\build.gradle (Module: app)`, добавив указанный ниже фрагмент. Этот код обеспечит использование приложением Пространственных привязок Azure версии 1.3.0. Впрочем, здесь подойдет любая актуальная версия Пространственных привязок Azure.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.3.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.3.0]"
    ...
}
```

Щелкните правой кнопкой мыши элемент `app\java\<PackageName>`->**New** (Создать) ->**Java Class** (Класс Java). Задайте для параметра **Name** (Имя) значение _MyFirstApp_, а для параметра **Superclass** (Суперкласс) — значение _android.app.Application_. Сохраните значения остальных параметров неизменными. Последовательно выберите **ОК**. Будет создан файл с именем `MyFirstApp.java`. Добавьте в него следующую инструкцию import:

```java
import com.microsoft.CloudServices;
```

Затем добавьте в новый класс `MyFirstApp` указанный ниже код, который выполняет инициализацию Пространственных привязок Azure с контекстом вашего приложения.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Измените файл `app\manifests\AndroidManifest.xml`, включив указанную ниже запись в корневой узел `<application>`. Этот код присоединит класс Application, который вы создали в приложении.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Вернитесь к файлу `app\java\<PackageName>\MainActivity` и добавьте в него следующие операторы import:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Затем добавьте следующие переменные-члены в класс `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Теперь добавим указанный ниже метод `initializeSession()` в класс `mainActivity`. При вызове он создает настройки, необходимые для создания и правильной инициализации сеанса Пространственных привязок Azure при запуске приложения.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Теперь подключим метод `initializeSession()` к методу `onCreate()`. Мы также настроим отправку кадров с камеры в пакет SDK Пространственных привязок Azure для обработки.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Наконец, добавьте указанный ниже код в метод `handleTap()`. Он присоединит локальную пространственную привязку службы "Пространственные привязки Azure" к черной сфере, которую мы размещаем в реальном мире.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Разверните](#trying-it-out) приложение еще раз. Переместите устройство, а затем коснитесь экрана для размещения черной сферы. Но на этот раз код создаст локальную пространственную привязку и прикрепит ее к созданной сфере.

Чтобы продолжить работу, вам следует создать идентификатор и ключ учетной записи Пространственных привязок Azure, если у вас их еще нет. Чтобы получить эти элементы, выполните инструкции из следующего раздела.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Отправка локальной привязки в облако

Получив идентификатор и ключ учетной записи Пространственных привязок Azure, вернитесь к файлу `app\java\<PackageName>\MainActivity` и добавьте в него следующие операторы import:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Затем добавьте следующие переменные-члены в класс `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Теперь добавьте указанный ниже код в метод `initializeSession()`. Этот код, прежде всего, позволяет приложению отслеживать, как пакет SDK Пространственных привязок Azure получает кадры от камеры. По мере выполнения этого процесса цвет сферы изменится с исходного черного на серый. Когда будет получено достаточное количество кадров, чтобы отправить привязку в облако, сфера станет белой. Во-вторых, этот код предоставляет необходимые учетные данные для взаимодействия с облачной серверной частью платформы. Именно здесь следует настроить идентификатор и ключ учетной записи, чтобы приложение могло их использовать. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов пространственных привязок](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Теперь добавьте указанный ниже метод `uploadCloudAnchorAsync()` в класс `mainActivity`. Этот метод асинхронно ожидает получения достаточного количества кадров с устройства. Когда информации будет достаточно, он переключит цвет сферы на желтый и начнет передавать локальную пространственную привязку в облако. После завершения отправки код возвращает идентификатор привязки.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Наконец, соединим все компоненты. Добавьте указанный ниже код в метод `handleTap()`. Он будет вызывать метод `uploadCloudAnchorAsync()` сразу после создания новой сферы. После завершения работы метода указанный ниже код выполнит последнее обновление параметров сферы, изменив ее цвет на синий.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Разверните](#trying-it-out) приложение еще раз. Переместите устройство, затем коснитесь экрана для размещения сферы. На этот раз вы увидите, как цвет сферы будет меняться с черного до белого по мере сбора кадров с камеры. Когда будет собрано достаточное количество кадров, цвет сферы станет желтым и начнется передача данных в облако. Когда отправка данных завершится, цвет сферы станет синим. При желании вы можете воспользоваться окном `Logcat` в Android Studio для мониторинга сообщений, которые приложение отправляет в журнал. Здесь вы увидите данные о ходе выполнения сеанса сбора кадров, а также идентификатор привязки, возвращаемый облаком после завершения передачи данных.

## <a name="locate-your-cloud-spatial-anchor"></a>Поиск облачной пространственной привязки

Когда привязка будет отправлена в облако, вы можете попытаться найти ее. Для этого добавим следующие операторы import в код приложения:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Теперь добавьте указанный ниже код в метод `handleTap()`. Этот код выполняет следующие действия:

- Удаляет с экрана синюю сферу.
- Повторно инициализирует сеанс Пространственных привязок Azure. Это действие гарантирует, что будет получена новая привязка из облака, а не созданная ранее локальная привязка.
- Направляет запрос на получение привязки, которую мы отправили в облако.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Теперь подключим код, который будет вызываться при обнаружении нужной привязки. Добавьте следующий код в метод `initializeSession()`: Этот фрагмент кода создает и размещает зеленую сферу, когда будет найдена облачная пространственная привязка. Также он снова включает отслеживание касания экрана, чтобы вы могли повторить весь процесс: создать новую локальную привязку, передать ее в облако и снова найти.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Вот и все! В последний раз [повторно разверните](#trying-it-out) приложение, чтобы полностью протестировать весь сценарий работы с ним. Переместите устройство, а затем разместите черную сферу. Продолжайте перемещать устройство, чтобы собрать достаточное количество кадров с камеры, пока сфера не станет желтой. Локальная привязка будет отправлена в облако, и цвет этой сферы изменится на синий. Наконец, еще раз коснитесь экрана, чтобы удалить локальную привязку, и попытайтесь получить облачную версию этой привязки. Продолжайте перемещать устройство, пока не обнаружите облачную пространственную привязку. В выбранном ранее месте появится зеленая сфера. Теперь вы можете повторить весь процесс.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
