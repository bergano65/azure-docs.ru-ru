---
title: Настройка проекта Unity с нуля
description: Узнайте, как настроить в пустом проекте Unity использование Удаленной отрисовки Azure.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: c05daa998829c4ac0687f75ae5678695127a50b0
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659916"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Руководство по Настройка проекта Unity с нуля

В этом руководстве описано следующее.

> [!div class="checklist"]
>
> * Настройка проекта Unity с нуля для использования Удаленной отрисовки Azure.
> * Создание и остановка сеансов отрисовки.
> * Повторное использование существующих сеансов.
> * Подключение к сеансам и отключение от них.
> * Загрузка моделей в сеанс отрисовки.
> * Отображение статистики подключения.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо следующее:

* Данные учетной записи (идентификатор и ключ учетной записи, идентификатор подписки). Если у вас нет учетной записи, [создайте ее](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(скачать)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* Последняя версия Visual Studio 2019 [(скачать)](https://visualstudio.microsoft.com/vs/older-downloads/). 
* [Средства Visual Studio для службы "Смешанная реальность"](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). В частности, обязательно установить следующие *рабочие нагрузки*:
  * **Разработка классических приложений на C++** .
  * **Разработка приложений для универсальной платформы Windows (UWP)** .
* GIT [(скачать)](https://git-scm.com/downloads).
* Unity 2019.3.1 [(скачать)](https://unity3d.com/get-unity/download).
  * Установите следующие модули в Unity:
    * **UWP** — обеспечивает поддержку для создания приложений универсальной платформы Windows;
    * **IL2CPP** — обеспечивает поддержку сборки для Windows (IL2CPP).

> [!TIP]
> [Репозиторий примеров для службы "Удаленная отрисовка Azure"](https://github.com/Azure/azure-remote-rendering) содержит подготовленные проекты Unity для всех руководств. Эти проекты вы можете использовать как справочные пособия.

## <a name="create-a-new-unity-project"></a>Создание проекта Unity

Создайте проект в Unity Hub.
В этом примере предполагается, что проект создается в папке с именем `RemoteRendering`.

![Окно нового проекта](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Настройка манифеста проекта

Необходимо изменить файл `Packages/manifest.json`, расположенный в папке проекта Unity. Откройте файл в текстовом редакторе и добавьте приведенные ниже строки:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Пакет Universal Render Pipeline (универсального конвейера обработки) является необязательным, но его рекомендуется использовать для повышения производительности.
После изменения и сохранения манифеста Unity автоматически обновится. Убедитесь, что пакеты загружены в окне *Project* (Проект):

![Подтверждение импорта пакетов](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Убедитесь, что у вас установлена последняя версия пакета.

Выполните следующие шаги, чтобы обеспечить использование последней версии пакета удаленной отрисовки для проекта.
1. Выберите пакет в окне проекта и щелкните значок :::no-loc text="package":::: ![Нажатие значка пакета](media/package-icons.png)
1. В окне Inspector щелкните View in Package Manager (Просмотр в диспетчере пакетов): ![Инспектор пакетов](media/package-properties.png)
1. На странице диспетчера пакетов выберите пакет удаленной отрисовки и проверьте, доступна ли кнопка обновления. Если это так, то при ее нажатии версия пакета будет обновлена до последней: ![Пакет Удаленной отрисовки Azure в диспетчере пакетов](media/package-manager.png)
1. Иногда обновление пакета может приводить к ошибкам в консоли. Если это происходит, попробуйте закрыть и снова открыть проект.

## <a name="configure-the-camera"></a>Настройка камеры

Выберите узел **Main Camera** (Основная камера).

1. Сбросьте *Transform* (Преобразование):

    ![Сброс преобразования камеры](media/camera-reset-transform.png)

1. Задайте свойству **:::no-loc text="Clear flags":::** значение *:::no-loc text="Solid Color":::* .

1. Задайте свойству **:::no-loc text="Background":::** значение *:::no-loc text="Black":::* .

1. Задайте свойству **:::no-loc text="Clipping Planes":::** значение *NEAR (Ближний) = 0.3* и *FAR (Дальний) = 20*. Это означает, что при отрисовке будет обрезаться геометрический объект, расположенный ближе чем 30 см или дальше 20 м.

    ![Свойства камеры Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Настройка параметров проекта

1. Выберите *Edit > Project Settings* (Изменить > Параметры проекта).
1. В списке слева выберите раздел Quality (Качество).
1. Для параметра **Active Quality Level** (Активный уровень качества) выберите значение *Low* (Низкий).
1. Измените настройки **Default Quality Level** (Уровень качества по умолчанию) на значение *Low* (Низкий).

    ![Изменение параметров качества в проекте](media/settings-quality.png)

1. В левой части экрана выберите раздел **Graphics** (Графика).
1. Измените параметр **Scriptable Rendering Pipeline** (Конвейер отрисовки с поддержкой скриптов), задав значение *HybridRenderingPipeline*. Пропустите этот шаг, если Universal Render Pipeline не используется.

    ![Изменение параметров графики в проекте](media/settings-graphics-lwrp.png) Иногда в пользовательском интерфейсе не заполняется список доступных типов конвейера из пакетов. В этом случае ресурс *HybridRenderingPipeline* необходимо перетащить в поле вручную: ![Изменение параметров графики в проекте](media/hybrid-rendering-pipeline.png)
1. Слева выберите раздел **Player** (Проигрыватель).
1. Перейдите на вкладку с **параметрами универсальной платформы Windows**.
1. Измените **XR Settings** (Параметры XR), чтобы настроить поддержку Windows Mixed Reality: ![Параметры проигрывателя](media/xr-player-settings.png)
1. Выберите параметры, как показано на снимке экрана выше:
    1. Установите флажок **Virtual Reality Supported** (Поддержка виртуальной реальности).
    1. Задайте для параметра **Depth Format** (Формат глубины) значение *16-Bit Depth* (Глубина 16 бит).
    1. Установите флажок **Depth Buffer Sharing** (Общий доступ к буферу глубины).
    1. Задайте для параметра **Stereo Rendering Mode** (Режим стерео отрисовки) значение *Single Pass Instanced* (Однопроходный экземпляр).

1. В этом же окне над *XR Settings* (Параметры XR) разверните меню **Publishing Settings** (Параметры публикации).
1. Прокрутите содержимое вниз до **Capabilities** (Возможности) и выберите:
    * **InternetClient**;
    * **InternetClientServer**;
    * **SpatialPerception**;
    * дополнительно для разработки можно выбрать **PrivateNetworkClientServer**.

      Этот параметр необходим, если вы хотите подключить к устройству удаленный отладчик Unity.

1. В разделе **Supported Device Families** (Поддерживаемые семейства устройств) установите флажки **Holographic** (Голографические) и **Desktop** (Компьютер).

1. Если вы хотите использовать набор средств для Смешанной реальности, см. [документацию по нему](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview), чтобы получить дополнительные сведения о возможностях и рекомендуемых параметрах.

## <a name="validate-project-setup"></a>Проверка настроек проекта

Выполните следующие действия, чтобы проверить правильность параметров проекта:

1. Выберите запись ValidateProject в меню RemoteRendering на панели инструментов редактора Unity.
1. При необходимости используйте окно ValidateProject для проверки и исправления параметров проекта.

    ![Проверка проекта редактора Unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Создание скрипта для инициализации Удаленной отрисовки Azure

Создайте [скрипт](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) с именем **RemoteRendering**. Откройте файл скрипта и замените его содержимое следующим кодом:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Этот скрипт инициализирует Удаленную отрисовку Azure, сообщает, какой объект камеры использовать для отрисовки, и помещает в окно просмотра кнопку **Create Session** (Создать сеанс) при запуске *Play Mode* (Режима воспроизведения).

> [!CAUTION]
> Изменение и сохранение скрипта в режиме воспроизведения в Unity может привести к зависанию средства. В таком случае необходимо принудительно завершить работу Unity через диспетчер задач. Поэтому всегда останавливайте воспроизведение, прежде чем изменять скрипт *RemoteRendering*.

## <a name="test-azure-remote-rendering-session-creation"></a>Проверка создания сеанса Удаленной отрисовки Azure

Создайте GameObject в сцене и добавьте в него компонент *RemoteRendering*. Заполните соответствующие поля *Учетная запись домена*, *Идентификатор учетной записи* и *Ключ учетной записи* для учетной записи Удаленной отрисовки:

![Свойства компонента Удаленной отрисовки](media/remote-rendering-component.png)

Запустите приложение в редакторе (**нажмите кнопку Play (Воспроизвести)** или клавиши CTRL+P). В окне просмотра появится кнопка **Create Session** (Создать сеанс). Нажмите ее, чтобы начать первый сеанс Удаленной отрисовки Azure:

![Создание первого сеанса](media/test-create.png)

Если произошел сбой, убедитесь, что вы правильно ввели учетные данные в свойствах компонента RemoteRendering. В противном случае в окне консоли появится сообщение с назначенным вам идентификатором сеанса, а также состоянием сеанса — *Starting* (Запуск):

![Выходные данные запуска сеанса](media/create-session-output.png)

На этом этапе Azure подготавливает сервер и запускает виртуальную машину для удаленной отрисовки. Обычно это **занимает 3–5 минут**. Когда виртуальная машина готова, выполняется обратный вызов сценария Unity `OnSessionStatusChanged` и выводится новое состояние сеанса:

![Выходные данные готовности сеанса](media/create-session-output-2.png)

Готово! Некоторое время больше ничего не будет происходить. Для предотвращения расходов следует всегда останавливать сеансы, если они больше не нужны. В этом примере сеанс можно остановить, нажав кнопку **Stop Session** (Остановить сеанс) или остановив имитацию Unity. Благодаря свойству **Auto-Stop Session** (Автоматическая остановка сеанса) в компоненте *ARRServiceUnity*, которое включено по умолчанию, сеанс будет автоматически останавливаться. Если что-то пойдет не так из-за сбоев или проблем с подключением, сеанс может выполняться до тех пор, пока позволяет параметр *MaxLeaseTime*, а затем работа будет завершена сервером.

> [!NOTE]
> Остановка сеанса произойдет мгновенно, и ее нельзя будет отменить. После остановки необходимо создать сеанс с теми же затратами на запуск.

## <a name="reusing-sessions"></a>Повторное использование сеансов

Создание сеанса, к сожалению, требует много времени. Поэтому следует создавать сеансы нечасто и использовать их повторно, когда это возможно.

Добавьте в скрипт *RemoteRendering* следующий код, а затем удалите старые версии дублирующихся функций:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Перед запуском этого кода убедитесь, что отключен параметр **Auto-Stop Session** (Автоматическая остановка сеанса) в компоненте RemoteRendering. В противном случае каждый создаваемый сеанс будет автоматически останавливаться при остановке имитации, а попытка его повторного использования будет приводить к сбою.

При нажатии кнопки *Play* (Воспроизвести) в окне просмотра появятся три кнопки: **Create Session** (Создать сеанс), **Query Active Sessions** (Запрос активных сеансов), **Use Existing Session** (Использовать существующий сеанс). Первая кнопка всегда позволяет создать сеанс. Вторая кнопка позволяет запросить существующие *активные* сеансы. Если вы не указали вручную идентификатор сеанса, который следует использовать, это действие повлечет автоматический выбор идентификатора сеанса для использования в будущем. С помощью третьей кнопки можно подключиться к существующему сеансу: к тому, который вы указали вручную в свойствах компонента *Session ID* (Идентификатор сеанса), либо к одному из найденных в результате *запрос активных сеансов*.

Функция **AutoStartSessionAsync** используется для имитации нажатий кнопки вне редактора.

> [!TIP]
> Можно открыть остановленные сеансы, а также сеансы, срок действия которых истек, или с состоянием ошибки. Хотя их больше нельзя использовать для отрисовки, можно запрашивать их данные после открытия неактивного сеанса. Приведенный выше код проверяет состояние сеанса в `ARRService_OnSessionStarted`, чтобы автоматически останавливать сеанс, когда его перестанут использовать.

С помощью этой функции вы теперь можете создавать и повторно использовать сеансы, что значительно повысит производительность в процессе разработки.

Как правило, создание сеанса будет запускаться не в клиентском приложении, так как на запуск сервера необходимо время.

## <a name="connect-to-an-active-session"></a>Подключение к активному сеансу

Ранее мы создавали или открывали сеансы. Следующий шаг — *подключение* к сеансу. После подключения сервер отрисовки создаст изображения и отправит поток видео в наше приложение.

Добавьте в скрипт *RemoteRendering* следующий код, а затем удалите старые версии дублирующихся функций:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Для проверки функции сделайте следующее:

1. Нажмите кнопку **Play** (Воспроизвести) в Unity.
1. Откройте сеанс:
    1. Если сеанс уже есть, нажмите кнопку **Query Active Sessions** (Запрос активных сеансов), а затем — **Use Existing Session** (Использовать существующий сеанс).
    1. Если у вас нет сеанса, нажмите кнопку **Create Session** (Создать сеанс).
1. Щелкните **Connect** (Подключить).
1. Через несколько секунд должны появиться выходные данные консоли, подтверждающие подключение.
1. Пока больше ничего не должно происходить.
1. Щелкните **Disconnect** (Отключить) или прервите режим воспроизведения в Unity.

>[!NOTE]
> Несколько пользователей могут *открыть* сеанс, чтобы запросить его данные, но только один пользователь может быть *подключен* к одному сеансу. Если другой пользователь уже подключен, другое подключение завершится сбоем с **ошибкой подтверждения**.

## <a name="load-a-model"></a>Загрузка модели

Добавьте в скрипт *RemoteRendering* следующий код, а затем удалите старые версии дублирующихся функций:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Теперь при нажатии кнопки воспроизведения, открытии сеанса и подключении к нему отображается кнопка **Load Model** (Загрузить модель). После ее нажатия в выходных данных консоли отображается ход загрузки, а по выполнении 100 % вы увидите модель двигателя:

![Модель, загруженная в редактор](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) является важным компонентом и используется, чтобы обеспечить [стабильность голограммы](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Но он работает только при развертывании на устройстве Смешанной реальности.

> [!TIP]
> Если вы следовали инструкциям из [краткого руководства по преобразованию модели для отрисовки](../../quickstarts/convert-model.md), то уже знаете, как преобразовать собственные модели. Все, что нужно сделать сейчас, чтобы преобразовать модель для просмотра, — это указать универсальный код ресурса (URI) преобразованной модели в свойстве *Model Name* (Имя модели).

## <a name="display-frame-statistics"></a>Отображение статистики кадров

Удаленная отрисовка Azure отслеживает различные сведения о качестве подключения. Чтобы быстро просматривать эти сведения, выполните следующие действия:

Создайте [скрипт](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) с именем **RemoteFrameStats**. Откройте файл скрипта и замените его содержимое следующим кодом:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Создайте GameObject и назовите его *FrameStats*. Вложите его как дочерний узел в объект *Main Camera* (Основная камера) и задайте для него значения расположения **x=0, y=0, z=0,325**. Добавьте в объект компонент **RemoteFrameStats**.

Добавьте дочерний объект **UI > Canvas** (Пользовательский интерфейс > Холст) к объекту *FrameStats* и задайте его свойства следующим образом:

![Свойства холста](media/framestats-canvas.png)

Добавьте дочерний объект холста **UI > Text** (Пользовательский интерфейс > Текст) и задайте его свойства следующим образом:

![Свойства текста](media/framestats-text.png)

Выберите объект *FrameStats* и заполните поле **FrameStats**, щелкнув значок круга и выбрав объект **Text** (Текст):

![Установка свойства текста](media/framestats-set-text.png)

Теперь при подключении к удаленному сеансу с помощью текста должна отображаться статистика потоковой передачи:

![Выходные данные статистики кадров](media/framestats-output.png)

Код отключает обновление статистики за пределами редактора, так как текстовое поле с заблокированным заголовком отвлекает от работы. Более сложную реализацию см. в [кратком руководстве](../../quickstarts/render-model.md) для проекта.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы ознакомились со всеми шагами, необходимыми для получения пустого проекта Unity и использования в нем Удаленной отрисовки Azure. В следующем учебнике мы более подробно рассмотрим, как работать с удаленными сущностями.

> [!div class="nextstepaction"]
> [Руководство. Работа с удаленными сущностями в Unity](working-with-remote-entities.md)
