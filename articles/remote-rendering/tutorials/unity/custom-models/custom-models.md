---
title: Интерфейсы и пользовательские модели
description: Добавление контроллеров представления и прием настраиваемых моделей с целью преобразования для просмотра с помощью службы "Удаленная отрисовка Azure"
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 5793e2958edce0a4c97660a75d0ecefa914c12d2
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559088"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Руководство по Интерфейсы и пользовательские модели

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Добавление набора средств Смешанной реальности в проект
> * Управление состоянием модели
> * Настройка Хранилища BLOB-объектов Azure для приема модели
> * Отправка и обработка моделей с целью преобразования для просмотра

## <a name="prerequisites"></a>Предварительные требования

* Перед изучением этого учебника ознакомьтесь с [учебником по просмотру модели удаленной отрисовки](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Начало работы с набором средств для Смешанной реальности

Набор средств для Смешанной реальности (MRTK) — это кроссплатформенный набор средств для создания функций смешанной реальности. Для реализации возможностей взаимодействия и визуализации мы будем использовать MRTK 2.5.1.

Чтобы добавить MRTK, выполните [обязательные шаги](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required), перечисленные в статье [Руководство по установке MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html).

К этим действиям относятся:
 - [Получение новейших пакетов Unity для MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Хотя он отмечен как "последний", используйте версию 2.5.1 на странице выпусков MRTK.
     - В этом руководстве используется только пакет *Foundation*. Пакеты *Extensions*, *Tools* и *Examples* не требуются.
 - [Импорт пакетов MRTK в проект Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Переключение проекта Unity на целевую платформу](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Этот шаг описан в первой главе и уже должен быть выполнен, но сейчас стоит это еще раз проверить.
 - [Добавление MRTK в новую сцену или новый проект](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Вы можете добавить MRTK в новую сцену, а затем повторно добавить координатор и объекты или скрипты модели либо добавить MRTK в существующую сцену с помощью команды меню *Mixed Reality Toolkit —> Add to Scene and Configure* (Набор средств для Смешанной реальности —> Добавить в сцену и настроить).

## <a name="import-assets-used-by-this-tutorial"></a>Импорт ресурсов, используемых в этом учебнике

В этой и последующих главах описано создание простого шаблона [модель — представление — контроллер](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) для большинства материалов. Часть *модель* этого шаблона представляет собой специальный код для службы "Удаленная отрисовка Azure" и отвечает за связанное с ней управление состоянием. Части *представление* и *контроллер* шаблона реализуются с помощью ресурсов MRTK и ряда пользовательских скриптов. При выполнении инструкций этого учебника можно использовать только *модель* без реализации части *представление — контроллер*. Такое разделение позволит легко интегрировать код из этого учебника в собственное приложение, в котором он будет выполняться вместо части конструктивного шаблона *представление — контроллер*.

С выпуском MRTK появилось множество скриптов, префабов и ресурсов, которые можно добавить в проект для обеспечения взаимодействия и визуальной обратной связи. Эти ресурсы, называемые **учебными**, объединяются в [пакет ресурсов Unity](https://docs.unity3d.com/Manual/AssetPackages.html), который размещен в [службе GitHub для Удаленной отрисовки Azure](https://github.com/Azure/azure-remote-rendering) в папке "\Unity\TutorialAssets\TutorialAssets.unitypackage".

1. Клонируйте или скачайте репозиторий Git для [Удаленной отрисовки Azure](https://github.com/Azure/azure-remote-rendering). В случае скачивания извлеките ZIP-файл в известное расположение.
1. В проекте Unity выберите *Assets —> Import Package —> Custom Package* (Ресурсы —> Импорт пакета —> Пользовательский пакет).
1. В проводнике перейдите к каталогу, в который клонирован или распакован репозиторий Удаленной отрисовки Azure, а затем выберите UNITYPACKAGE-файл, расположенный в папке **Unity —> TutorialAssets —> TutorialAssets.unitypackage**.
1. Нажмите кнопку **Import** (Импорт), чтобы импортировать содержимое пакета в свой проект.
1. В редакторе Unity в верхней строке меню выберите *Mixed Reality Toolkit —> Utilities —> Upgrade MRTK Standard Shader for Lightweight Render Pipeline* (Набор средств для Смешанной реальности —> Служебные программы —> Обновить стандартный шейдер MRTK для упрощенного конвейера отрисовки) и следуйте инструкциям по обновлению шейдера.

После включения MRTK и учебных ресурсов в проект давайте сменим профиль MRTK на более подходящий для данного учебника.

1. В иерархии сцены выберите игровой объект **MixedRealityToolkit**.
1. В инспекторе в компоненте **MixedRealityToolkit** смените профиль конфигурации на *ARRMixedRealityToolkitConfigurationProfile*.
1. Нажмите клавиши *CTRL+S*, чтобы сохранить изменения.

В результате набор MRTK будет настроен с использованием преимущественно профилей HoloLens 2 по умолчанию. Указанные профили предварительно настроены, как описано ниже.
 - Профилировщик отключен (нажмите клавишу 9, чтобы его включить или отключить, или скажите Show/Hide Profiler (Показать/скрыть профилировщик) на устройстве).
 - Курсор взгляда отключен.
 - Щелчки мыши Unity включены, поэтому для выбора элементов пользовательского интерфейса MRTK вместо имитации руки можно использовать мышь.

## <a name="add-the-app-menu"></a>Добавление меню приложения

Большинство контроллеров представления, описанных в этом учебнике, работают с базовыми абстрактными, а не конкретными классами. Этот шаблон обеспечивает большую гибкость и позволяет нам предоставлять контроллеры представления, помогая при этом изучать код Удаленной отрисовки Azure. Для простоты класс **RemoteRenderingCoordinator** не имеет абстрактного класса и его контроллер представления работает с конкретным классом напрямую.

Теперь можно добавить в сцену префаб **AppMenu**, чтобы обеспечить визуальную обратную связь для текущего состояния сеанса. Этот контроллер представления будет обеспечивать доступ к другим контроллерам представления подменю по мере реализации дополнительных функций Удаленной отрисовки Azure и интеграции их в сцену. Теперь **AppMenu** будет иметь визуальную индикацию состояния службы "Удаленная отрисовка Azure" и отображать модальную панель, которую пользователь использует для авторизации приложения при подключении к этой службе.

1. Найдите префаб **AppMenu** в папке *Assets/RemoteRenderingTutorial/Prefabs/AppMenu*.
1. Перетащите префаб **AppMenu** на сцену.
1. Скорее всего, вы увидите диалоговое окно **средства импорта TMP**, так как ресурс *Text Mesh Pro* добавляется в сцену в первый раз. Следуйте инструкциям, чтобы **импортировать основные компоненты TMP**. Затем закройте диалоговое окно средства импорта. Примеры и дополнительные компоненты не нужны.
1. Префаб **AppMenu** настроен для автоматической привязки и предоставления модальных средств получения согласия на подключение к сеансу, поэтому мы можем удалить средство обхода, размещенное ранее. Удалите из игрового объекта **RemoteRenderingCoordinator** средство обхода авторизации, реализованное ранее. Для этого нажмите кнопку "-" для события **On Requesting Authorization** (При запросе авторизации).
 ![Удаление средства обхода.](./media/remove-bypass-event.png)
1. Проверьте контроллер представления, нажав **Play** (Воспроизвести) в редакторе Unity.
1. Теперь, когда набор MRTK настроен, вы можете менять расположение представления с помощью клавиш W, A, S и D, а также изменять его направление, перемещая указатель мыши и удерживая нажатой ее правую кнопку. Поэкспериментируйте со сценой, чтобы освоиться с управлением.
1. Чтобы вызвать **AppMenu**, можно поднять ладонь вверх на устройстве или использовать клавишу доступа "M" в редакторе Unity.
1. Если вы потеряли меню из виду, нажмите клавишу "M", чтобы его вызвать. Для удобства взаимодействия меню будет размещено рядом с камерой.
1. Теперь для авторизации будет отображаться запрос справа от **AppMenu**. Вы будете использовать его, чтобы авторизовать приложение для управления сеансами удаленной отрисовки.
 ![Авторизация пользовательского интерфейса](./media/authorize-request-ui.png)
1. Остановите воспроизведение в Unity, чтобы продолжить работу с учебником.

## <a name="manage-model-state"></a>Управление состоянием модели

Теперь мы реализуем новый скрипт **RemoteRenderedModel**, предназначенный для отслеживания состояния, реагирования на события, инициирования событий и настройки. По сути, **RemoteRenderedModel** сохраняет удаленный путь для данных модели в `modelPath`. Он будет ожидать передачи данных об изменении состояния в **RemoteRenderingCoordinator**, чтобы определить, следует ли автоматически загружать или выгружать модель, которую он определяет. Игровой объект, к которому присоединен скрипт **RemoteRenderedModel**, будет выполнять функцию локального родительского элемента для удаленного содержимого.

Обратите внимание, что скрипт **RemoteRenderedModel** реализует элемент **BaseRemoteRenderedModel**, добавленный из **учебных ресурсов**. Он позволит удаленному контроллеру представления модели выполнить привязку к скрипту.

1. Создайте скрипт с именем **RemoteRenderedModel** в той же папке, что и **RemoteRenderingCoordinator**. Замените все его содержимое кодом, приведенным ниже.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

Вообще говоря, **RemoteRenderedModel** содержит данные, необходимые для загрузки модели (в данном случае SAS или *builtin://* URI), и позволяет отслеживать состояние удаленной модели. Если необходимо загрузить модель, для **RemoteRenderingCoordinator** вызывается метод `LoadModel` и возвращается содержащая модель сущность, которую можно использовать для ссылки на модель и ее выгрузки.

## <a name="load-the-test-model"></a>Загрузка тестовой модели

Давайте протестируем новый скрипт, загрузив тестовую модель снова. Мы создадим игровой объект, который будет содержать скрипт и станет родительским для тестовой модели.

1. Создайте пустой игровой объект в сцене и назовите его **TestModel**.
1. Добавьте скрипт *RemoteRenderedModel* в **TestModel**.
![Добавление компонента RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Заполните поля `Model Display Name` и `Model Path` значениями *TestModel* и *builtin://Engine* соответственно.
![Настройка параметров модели](./media/add-model-script.png)
1. Поместите объект **TestModel** перед камерой в положении **x = 0, y = 0, z = 3**.
![Размещение объекта](./media/test-model-position.png)
1. Убедитесь, что функция **AutomaticallyLoad** включена.
1. Нажмите **Play** (Воспроизвести) в редакторе Unity, чтобы протестировать приложение.
1. Предоставьте разрешение для авторизации, нажав кнопку *Connect* (Подключить), чтобы разрешить приложению создать сеанс. Оно подключится к этому сеансу и автоматически загрузит модель.

Наблюдайте за консолью по мере смены состояний продвижения. Помните, что для прохождения некоторых состояний может потребоваться определенное время, в течение которого вы не будете видеть никаких изменений. По сути, вы будете видеть журналы загрузки модели, после чего тестовая модель будет преобразована для просмотра в сцене.

Попробуйте перемещать и вращать игровой объект **TestModel** с помощью преобразования в инспекторе или представлении сцены. При этом вы будете видеть, как модель перемещается и вращается в игровом представлении.

![Журнал Unity](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Подготовка Хранилища BLOB-объектов в Azure и прием настраиваемых моделей

Теперь можно попробовать загрузить собственную модель. Для этого необходимо настроить Хранилище BLOB-объектов, передать и преобразовать модель в Azure, а затем загрузить модель с помощью скрипта **RemoteRenderedModel**. Если в настоящее время у вас нет собственной модели для загрузки, можно просто пропустить действия по загрузке настраиваемой модели.

Выполните действия, указанные в документе [Краткое руководство. Преобразование модели для отрисовки](../../../quickstarts/convert-model.md). Для целей этого учебника не требуется выполнять инструкции в разделе **Вставка новой модели в пример приложения из краткого руководства**, поэтому пропустите его. После получения универсального кода ресурса (URI) *подписанного URL-адреса (SAS)* принятой модели перейдите к следующему шагу.

## <a name="load-and-rendering-a-custom-model"></a>Загрузка настраиваемой модели и ее преобразование для просмотра

1. Создайте пустой игровой объект в сцене и назовите его так же, как настраиваемую модель.
1. Добавьте скрипт *RemoteRenderedModel* в созданный игровой объект.
 ![Добавление компонента RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Заполните поле `Model Display Name` соответствующим именем модели.
1. В поле `Model Path` укажите универсальный код ресурса *подписанного URL-адреса* модели, созданный на этапах приема, описанных выше.
1. Поместите игровой объект перед камерой в положении **x = 0, y = 0, z = 3**.
1. Убедитесь, что функция **AutomaticallyLoad** включена.
1. Нажмите **Play** (Воспроизвести) в редакторе Unity, чтобы протестировать приложение.

    Вы увидите, как консоль начинает заполняться текущим состоянием, а затем появятся сообщения о ходе выполнения загрузки модели. Настраиваемая модель будет загружена в сцену.

1. Удалите объект настраиваемой модели из сцены. Для работы с этим руководством рекомендуется использовать тестовую модель. Хотя на самом деле в службе "Удаленная отрисовка Azure" поддерживается несколько моделей, материал этого учебника наилучшим образом подходит для работы с одной удаленной моделью.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы можете загружать собственные модели в службу "Удаленная отрисовка Azure" и просматривать их в своем приложении. Далее мы расскажем вам, как манипулировать моделями.

> [!div class="nextstepaction"]
> [Дальнейшие действия. Манипулирование моделями](../manipulate-models/manipulate-models.md)
