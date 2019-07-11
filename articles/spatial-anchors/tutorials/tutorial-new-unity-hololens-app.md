---
title: Руководство. Пошаговые инструкции по созданию приложения HoloLens Unity с использованием Пространственных привязок Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как создать приложение HoloLens Unity с использованием Пространственных привязок Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c831e8fdacf5103619374605dd980ab1f6735047
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135310"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Руководство по созданию приложения HoloLens Unity с использованием Пространственных привязок Azure

В этом учебнике описывается, как создать приложение HoloLens Unity с помощью Пространственных привязок Azure.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются:

1. Компьютер Windows с установленной программой <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> с рабочей нагрузкой **Разработка приложений для универсальной платформы Windows** и компонентом **Пакет SDK для Windows 10 (10.0.17763.0 или более поздняя версия)** , а также <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>.
2. Установленное [расширение Visual Studio C++/WinRT](https://aka.ms/cppwinrt/vsix) из [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Устройство HoloLens с включенным [режимом разработчика](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). Для этой статьи требуется устройство HoloLens с [обновлением Windows 10 за октябрь 2018 г.](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (также известным как RS5). Чтобы обновить HoloLens до последней версии, откройте приложение **Параметры**, перейдите в раздел **Обновление и безопасность**, а затем нажмите кнопку **Проверить обновления**.

## <a name="getting-started"></a>Начало работы

Сначала мы настроим проект и сцену Unity.
1. Запустите Unity.
2. Нажмите кнопку **Создать**.
4. Убедитесь, что выбран параметр **3D**.
5. Укажите имя проекта и введите **Расположение** для сохранения.
6. Нажмите кнопку **Создать проект**.
7. Сохраните пустую сцену по умолчанию в новый файл, используя: **Файл** > **Сохранить как**.
8. Назовите новую сцену **Главная** и нажмите кнопку **Сохранить**.

**Настройка параметров проекта**

Теперь мы установим некоторые параметры проекта Unity, которые помогут нам выбрать пакет SDK Windows Holographic для разработки. 

Сначала установим параметры качества для приложения. 
1. Выберите **Изменить** > **Параметры проекта** > **Качество**
2. В столбце под логотипом **Магазин Windows** щелкните стрелку в строке **По умолчанию** и выберите **Очень низкое**. Вы узнаете, что этот параметр применяется правильно, когда поле в столбце **Магазин Windows** и строка **Очень низкое** станут зеленого цвета.

Необходимо сообщить Unity, что приложение, которое мы пытаемся экспортировать, должно создать иммерсивное представление вместо двухмерного. Мы создадим иммерсивное представление, включив поддержку виртуальной реальности в Unity, ориентированном на пакет SDK для Windows 10.

1. Выберите **Изменить** > **Параметры проекта** > **Проигрыватель**.
2. На панели **Инспектор** для параметра **Настройки проигрывателя** выберите значок **Магазин Windows**.
3. Разверните группу **XR Settings** (Параметры XR).
4. В разделе **Отрисовка** установите флажок **Virtual Reality Supported** (Поддержка виртуальной реальности), чтобы добавить новый список **Virtual Reality SDK's** (SDK виртуальной реальности).
5. Убедитесь, что **Windows Mixed Reality** отображается в списке. Если нет, нажмите кнопку **+** внизу списка и выберите **Windows Mixed Reality**.
 
> [!NOTE]
> Если значок "Магазин Windows" не отображается, проверьте все еще раз, чтобы убедится, что перед установкой вы выбрали серверную часть сценариев .NET Магазина Windows. В противном случае может потребоваться переустановить Unity с правильной установкой Windows.

**Проверка конфигурации .NET**
1. Выберите **Изменить** > **Параметры проекта** > **Проигрыватель** (**Проигрыватель** все еще может быть открыт из предыдущего шага).
2. На панели **Инспектор** для параметра **Настройки проигрывателя** выберите значок **Магазин Windows**.
3. В разделе конфигурации **Другие параметры** убедитесь, что для параметра **Scripting Backend** (Серверная часть сценария) установлено значение **.NET**.

**Установка возможностей**
1. Выберите **Изменить** > **Параметры проекта** > **Проигрыватель** (**Проигрыватель** все еще может быть открыт из предыдущего шага).
2. На панели **Инспектор** для параметра **Настройки проигрывателя** выберите значок **Магазин Windows**.
3. В разделе конфигурации **Параметры публикации** проверьте **InternetClientServer** и **SpatialPerception**.

**Настройка главной виртуальной камеры**
1. На панели **Иерархия** выберите объект **Main Camera**.
2. На панели **Инспектор** задайте для положения преобразования значение **0,0,0**.
3. Найдите свойство **Clear Flags** (Очистить флаги) и измените раскрывающийся список из **Skybox** на **Solid Color** (Сплошной цвет).
4. Щелкните поле **Фон**, чтобы открыть палитру.
5. Задайте для **R, G, B, and A** (Красный, зеленый, синий и альфа-компонент) значение **0**.
6. Выберите **Добавить компонент** и найдите **Spatial Mapping Collider** (Коллайдер пространственных сопоставлений).

**Создание сценария**
1. На панели **Проект** создайте папку с именем **Сценарии** в папке **Ресурсы**. 
2. Щелкните папку правой кнопкой мыши и выберите **Создать** > **Сценарий C#** . Назовите его **AzureSpatialAnchorsScript**. 
3. Выберите **GameObject** -> **Create Empty** (Создать пустой). 
4. Выберите его и на панели **Инспектор** переименуйте из **GameObject** в **MixedRealityCloud**. Выберите **Добавить компонент**, а затем найдите и добавьте **AzureSpatialAnchorsScript**.

## <a name="trying-it-out"></a>Проверка работы
Чтобы проверить, что все работает, создайте приложение в **Unity** и разверните его из **Visual Studio**. Для этого выполните указания в главе 6 курса [**MR Basics 100: Getting started with Unity**](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) (Основы смешанной реальности: приступая к работе с Unity). Вы должны увидеть начальный экран Unity, а затем чистый экран.

## <a name="place-an-object-in-the-real-world"></a>Размещение объекта в реальном мире
Давайте создадим новый объект и разместим его с помощью приложения. Откройте решение Visual Studio, созданное при [развертывании приложения](#trying-it-out). 

Сначала добавьте следующие операторы import в файл `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Затем добавьте следующие переменные-члены в класс `AzureSpatialAnchorsScript`: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-37,43-47,55-74)]

Теперь добавьте указанный ниже код в метод `Start()`. Этот код подключит `GestureRecognizer`, который обнаружит касание и вызовет `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-85,88&highlight=4-10)]

Теперь необходимо добавить следующий метод `HandleTap()` под `Update()`. Он выполнит "бросание лучей" и получит точку попадания, в которой нужно разместить сферу. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-274,295-297,301-309)]

Теперь необходимо создать сферу. Изначально сфера будет белой, но позже это значение можно будет изменить. Добавьте следующий метод `CreateAndSaveSphere()`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-324,389)]

Запустите приложение из **Visual Studio**, чтобы проверить его еще раз. На этот раз коснитесь экрана, чтобы создать белую сферу и поместить ее на выбранную поверхность.

## <a name="set-up-the-dispatcher-pattern"></a>Настройка шаблона диспетчера

При работе с Unity все API-интерфейсы Unity, например API-интерфейсы для обновлений пользовательских интерфейсов, должны выполнятся в основном потоке. Однако в коде, который мы напишем, мы получаем обратные вызовы в других потоках. Мы хотим обновить пользовательский интерфейс в этих обратных вызовах, поэтому нам нужен способ, чтобы перейти из бокового потока в основной. Чтобы выполнить код в основном потоке из бокового потока, мы используем шаблон диспетчера. 

Добавим переменную-член dispatchQueue, которая является очередью действий. Мы поместим действия в очередь, а затем выведем из очереди и запустим действия в основном потоке. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=33-46&highlight=6-9)]

Затем добавим способ, чтобы добавить действие в очередь. Добавьте `QueueOnUpdate()` сразу после `Update()`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=102-112)]

Теперь давайте используем цикл Update(), чтобы убедится, что действие находится в очереди. Если да, мы выведем действие из очереди и запустим его.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=90-100&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Получение пакета SDK Пространственных привязок Azure

Теперь мы скачаем пакет SDK Пространственных привязок Azure. Перейдите на [страницу выпусков Пространственных привязок Azure на GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). В разделе "Ресурсы" скачайте файл **AzureSpatialAnchors.unitypackage**. 

В Unity перейдите к разделу **Ресурсы** и щелкните **Import Package** (Импорт пакета)  > **Custom Package...** (Пользовательский пакет...). Перейдите к пакету и выберите **Открыть**.

В появившемся окне **Import Unity Package** (Импорт пакета Unity) выберите **Нет** слева внизу. Затем в разделе **AzureSpatialAnchorsPlugin** > **Plugins** (Подключаемые модули) выберите **Общий**, **Редактор** и **HoloLens**. В нижнем правом углу щелкните **Импорт**.

Теперь необходимо восстановить пакеты Nuget, чтобы получить пакет SDK Пространственных привязок Azure. Создайте из **Unity**, а затем откройте и снова создайте результирующее решение **Visual Studio**, как описано в разделе о [проверке работы](#trying-it-out). 

В решении **Visual Studio** добавьте следующую операцию импорта в `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Затем добавьте следующие переменные-члены в класс `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-58&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Присоединение локальной пространственной привязки Azure к локальной привязке

Давайте настроим CloudSpatialAnchorSession Пространственной привязки Azure. Для начала добавим следующий метод `InitializeSession()` в класс `AzureSpatialAnchorsScript`. При вызове он создает настройки, необходимые для создания и правильной инициализации сеанса Пространственных привязок Azure при запуске приложения.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=169-197,200-204)]

Теперь необходимо написать код, чтобы обработать вызовы делегата. Мы добавим к ним больше по мере продолжения.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-221)]

Теперь подключим метод `initializeSession()` к методу `Start()`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-88&highlight=12)]

Наконец, добавьте указанный ниже код в метод `CreateAndSaveSphere()`. Он присоединит локальную пространственную привязку службы "Пространственные привязки Azure" к сфере, которую мы размещаем в реальном мире.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-337,390&highlight=16-31)]

Чтобы продолжить работу, вам следует создать идентификатор и ключ учетной записи Пространственных привязок Azure, если у вас их еще нет. Чтобы получить эти элементы, выполните инструкции из следующего раздела.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Отправка локальной привязки в облако

Получив идентификатор учетной записи и ключ Пространственных привязок Azure, перейдите и вставьте `Account Id` в `SpatialAnchorsAccountId` и `Account Key` в `SpatialAnchorsAccountKey`.

Наконец, соединим все компоненты. Добавьте указанный ниже код в метод `SpawnNewAnchoredObject()`. Он будет вызывать метод `CreateAnchorAsync()` сразу после создания сферы. После завершения работы метода указанный ниже код выполнит последнее обновление параметров сферы, изменив ее цвет на синий.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-389&highlight=28-78)]

Запустите приложение из **Visual Studio** еще раз. Покрутите головой, а затем коснитесь, чтобы разместить сферу. Когда будет собрано достаточное количество кадров, цвет сферы станет желтым и начнется передача данных в облако. Когда отправка данных завершится, цвет сферы станет синим. При желании вы можете воспользоваться окном вывода в **Visual Studio** для мониторинга сообщений, которые приложение отправляет в журнал. Вы сможете просмотреть рекомендованный прогресс создания, а также идентификатор привязки, который вернет облако после завершения отправки.

> [!NOTE]
> Если вы получаете сообщение DllNotFoundException: Unable to load DLL 'AzureSpatialAnchors': The specified module could not be found. (DllNotFoundException. Не удается загрузить DLL "AzureSpatialAnchors": не найден указанный модуль.) необходимо **очистить** и снова **создать** решение.

## <a name="locate-your-cloud-spatial-anchor"></a>Поиск облачной пространственной привязки

Когда привязка будет отправлена в облако, вы можете попытаться найти ее. Добавьте указанный ниже код в метод `HandleTap()`. Этот код выполняет следующие действия:

* Вызовите `ResetSession()`, который остановит `CloudSpatialAnchorSession` и удалит с экрана синюю сферу.
* Инициализируйте `CloudSpatialAnchorSession` снова. Это гарантирует, что будет получена новая привязка из облака, а не созданная ранее локальная привязка.
* Создайте **наблюдателя**, который будет искать привязку, загруженную в Пространственные привязки Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-302&highlight=13-31,34-36)]

Теперь добавим методы `ResetSession()` и `CleanupObjects()`. Их можно поместить ниже `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=114-167)]

Теперь необходимо подключить код, который будет вызываться при обнаружении нужной привязки. Внутри `InitializeSession()` добавьте следующие обратные вызовы:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=195-201&highlight=4-5)]

 
Теперь добавим код, который создаст и разместит зеленую сферу, когда будет найден CloudSpatialAnchor. Также он снова включает отслеживание касания экрана, чтобы вы могли повторить весь процесс: создать новую локальную привязку, передать ее в облако и снова найти.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=223-262)]

Вот и все! В последний раз запустите приложение из **Visual Studio**, чтобы полностью протестировать весь сценарий работы с ним. Переместите устройство, а затем разместите белую сферу. Продолжайте двигать головой, чтобы собрать достаточное количество данных окружения, пока сфера не станет желтой. Локальная привязка будет отправлена в облако, и цвет этой сферы изменится на синий. Наконец, еще раз коснитесь экрана, чтобы удалить локальную привязку, и попытайтесь получить облачную версию этой привязки. Продолжайте перемещать устройство, пока не обнаружите облачную пространственную привязку. В выбранном ранее месте появится зеленая сфера. Теперь вы можете повторить весь процесс.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]