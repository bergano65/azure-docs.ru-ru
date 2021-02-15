---
title: Интеграция Удаленной отрисовки в голографическое приложение C++/DirectX11
description: Объяснение процесса интеграции Удаленной отрисовки в простое голографическое приложение C++/DirectX11, созданное с помощью мастера проектов Visual Studio
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: a07a8a9c50e0f71daa48f65ebf8c2e7a7f166cc5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594305"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Руководство по Интеграция Удаленной отрисовки в голографическом приложении HoloLens

В этом руководстве описано следующее.

> [!div class="checklist"]
>
> * Использование Visual Studio для создания голографического приложения, которое можно развернуть в HoloLens
> * Добавление необходимых фрагментов кода и параметров проекта для объединения локальной отрисовки с удаленно отрисованным содержимым

В этом учебнике рассматривается добавление необходимых составляющих в пример собственного приложения `Holographic App` для объединения локальной отрисовки с Удаленной отрисовкой Azure. Единственным средством предоставления сведений о состоянии в этом приложении служит панель выходных данных отладки в Visual Studio, поэтому данный пример рекомендуется запускать из Visual Studio. Добавление надлежащего встроенного средства предоставления сведений в приложении выходит за рамки данного примера, поскольку создание динамической текстовой панели с нуля требует значительных объемов кода. Хорошей отправной точкой служит класс `StatusDisplay`, который является частью [примера проекта удаленного проигрывателя на сайте GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). На самом деле в предварительно подготовленной версии этого учебника используется локальная копия данного класса.

> [!TIP]
> В [репозитории примеров маршрутизации запросов приложений](https://github.com/Azure/azure-remote-rendering) содержится результат выполнения инструкций этого учебника в виде готового к использованию проекта Visual Studio. Кроме того, он дополнен надлежащим средством предоставления отчетов об ошибках и состоянии, которые реализуются через класс пользовательского интерфейса `StatusDisplay`. В этом учебнике все дополнения, относящиеся к маршрутизации запросов приложений, ограничены `#ifdef USE_REMOTE_RENDERING` / `#endif`, поэтому дополнения для Удаленной отрисовки можно легко определить.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо следующее:

* Данные учетной записи (идентификатор, ключ и домен учетной записи, идентификатор подписки). Если у вас нет учетной записи, [создайте ее](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(скачать)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* Последняя версия Visual Studio 2019 [(скачать)](https://visualstudio.microsoft.com/vs/older-downloads/).
* [Средства Visual Studio для службы "Смешанная реальность"](/windows/mixed-reality/install-the-tools). В частности, обязательно установить следующие *рабочие нагрузки*:
  * **Разработка классических приложений на C++** .
  * **Разработка приложений для универсальной платформы Windows (UWP)** .
* Шаблоны приложений Смешанной реальности для Windows для Visual Studio [(скачать)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Пример создания нового голографического приложения

Для начала мы создадим базовый пример, который служит основой для интеграции с Удаленной отрисовкой. Откройте Visual Studio, выберите "Создать проект" и выполните поиск фразе "голографическое приложение DirectX 11 (универсальное для Windows) (C++/WinRT)".

![Создание нового проекта](media/new-project-wizard.png)

Введите имя проекта по своему усмотрению, выберите путь и нажмите кнопку "Создать".
В новом проекте переведите конфигурацию в режим **Отладка/ARM64**. Теперь вы можете скомпилировать его и развернуть на подключенном устройстве HoloLens 2. Если вы запустите его в HoloLens, то увидите перед собой вращающийся куб.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Добавление зависимостей Удаленной отрисовки с помощью NuGet

Первое, что нужно сделать для добавления возможностей Удаленной отрисовки, — добавить зависимости на стороне клиента. Соответствующие зависимости доступны в виде пакета NuGet.
В Обозревателе решений щелкните правой кнопкой мыши проект и выберите в контекстном меню **Управление пакетами NuGet...** .

В диалоговом окне с запросом найдите пакет NuGet с именем **Microsoft.Azure.RemoteRendering.Cpp**.

![Переход к пакету NuGet](media/add-nuget.png)

Добавьте его в проект, выбрав пакет и нажав кнопку "Установить".

Пакет NuGet добавляет в проект зависимости Удаленной отрисовки. В частности:
* устанавливает связь с клиентской библиотекой (RemoteRenderingClient.lib);
* настраивает зависимости .dll;
* задает правильный путь к каталогу для директивы include.

## <a name="project-preparation"></a>Подготовка проекта

Нам нужно внести небольшие изменения в имеющийся проект. Эти изменения несущественны, но без их Удаленная отрисовка не будет работать.

### <a name="enable-multithread-protection-on-directx-device"></a>Включение многопотоковой защиты на устройстве DirectX
На устройстве `DirectX11` должна быть включена многопотоковая защита. Чтобы изменить соответствующие настройки, откройте файл DeviceResources.cpp в папке Common и вставьте следующий код в конец функции `DeviceResources::CreateDeviceResources()`:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Включение сетевых возможностей в манифесте приложения
Для развернутого приложения необходимо явно включить сетевые возможности. Без такой настройки для запросов на подключение будет постоянно истекать время ожидания. Чтобы включить эти возможности, дважды щелкните элемент `package.appxmanifest` в обозревателе решений. На следующем экране пользовательского интерфейса перейдите на вкладку **Возможности** и выберите:
* Интернет (клиент и сервер)
* Интернет (клиент)

![Сетевые возможности](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Интеграция Удаленной отрисовки

Теперь, когда проект подготовлен, мы можем приступить к работе с кодом. Хорошей точкой входа в приложение будет класс `HolographicAppMain` (файл HolographicAppMain.h/cpp), так как он содержит все необходимые обработчики для инициализации и ее отмены инициализации, а также отрисовки.

### <a name="includes"></a>Includes

Начнем с добавления необходимых директив include. Добавьте следующую директиву include в файл HolographicAppMain.h:

```cpp
#include <AzureRemoteRendering.h>
```

Добавьте также эти дополнительные директивы `include` в файл HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

Для простоты кода мы определяем следующий ярлык пространства имен в начале файла HolographicAppMain.h после директив `include`:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Этот ярлык удобен тем, что с ним нам не придется постоянно писать полное пространство, и при этом структуры данных, связанные с маршрутизацией запросов приложений будут распознаваться. Конечно, мы также можем использовать директиву `using namespace...`.

### <a name="remote-rendering-initialization"></a>Инициализация Удаленной отрисовки
 
В течение всего времени существования приложения необходимо хранить несколько объектов для сеанса и т. д. Время существования совпадает со временем существования объекта `HolographicAppMain` приложения, поэтому мы добавляем объекты в качестве членов класса `HolographicAppMain`. Следующим шагом будет добавление приведенных ниже членов класса в файл HolographicAppMain.h.

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::RemoteRenderingClient> m_client;  // the client instance
    RR::ApiHandle<RR::RenderingSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RenderingConnection> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Connection()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Для фактической реализации отлично подойдет конструктор класса `HolographicAppMain`. В этом случае необходимо выполнить инициализацию трех типов:
1. однократную инициализацию системы Удаленной отрисовки;
1. Создание клиентов (аутентификация)
1. создание сеанса.

Все это выполняется последовательно в конструкторе. Однако в реальных вариантах использования может быть целесообразно выполнить эти действия отдельно.

Добавьте следующий код в начало тела конструктора в файле HolographicAppMain.cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.ConnectionType = RR::ConnectionType::General;
        clientInit.GraphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.ToolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.UnitsPerMeter = 1.0f;
        clientInit.Forward = RR::Axis::NegativeZ;
        clientInit.Right = RR::Axis::X;
        clientInit.Up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create Client
    {
        // Users need to fill out the following with their account data and model
        RR::SessionConfiguration init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
        m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
    }

    // 3. Open/create rendering session
    {
        auto SessionHandler = [&](RR::Status status, RR::ApiHandle<RR::CreateRenderingSessionResult> result)
        {
            if (status == RR::Status::OK)
            {
                auto ctx = result->GetContext();
                if (ctx.Result == RR::Result::Success)
                {
                    SetNewSession(result->GetSession());
                }
                else
                {
                    SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                }
            }
            else
            {
                SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
            }
        };

        // If we had an old (valid) session that we can recycle, we call async function m_client->OpenRenderingSessionAsync
        if (!m_sessionOverride.empty())
        {
            m_client->OpenRenderingSessionAsync(m_sessionOverride, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
        else
        {
            // create a new session
            RR::RenderingSessionCreationOptions init;
            init.MaxLeaseInMinutes = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            m_client->CreateNewRenderingSessionAsync(init, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

Код вызывает функции элементов `SetNewSession` и `SetNewState`, реализацию которых мы рассмотрим в следующем абзаце вместе с остальной частью кода конечного автомата.

Обратите внимание, что в примере жестко закодированы учетные данные. Поэтому их необходимо подставить ([идентификатор учетной записи, ключ учетной записи, домен учетной записи](../../../how-tos/create-an-account.md#retrieve-the-account-information) и [домен удаленной отрисовки](../../../reference/regions.md)).

Инициализацию мы отменяем симметрично и делаем это в обратном порядке в конце тела деструктора.

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->Disconnect();
        m_session = nullptr;
    }

    // Destroy front end:
    m_client = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Конечный автомат

В Удаленной отрисовке асинхронные функции являются ключевыми для создания сеанса и загрузки модели. Чтобы это учесть, нам нужен простой конечный автомат, который, по сути, автоматически переходит между следующими состояниями:

*инициализация —> создание сеанса —> запуск сеанса —> загрузка модели (с ходом выполнения)* .

Соответственно, на следующем шаге мы добавим в класс некоторые возможности обработки конечного автомата. Мы объявим собственное перечисление `AppConnectionStatus` для различных состояний, в которых может находится приложение. Оно аналогично перечислению `RR::ConnectionStatus`, но содержит дополнительное состояние для сбоя подключения.

Добавьте в объявление класса следующие члены и функции:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

На стороне реализации в CPP-файле добавьте следующие тела функций:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSasOptions options;
    options.ModelUri = m_modelURI.c_str();
    options.Parent = nullptr;

    // start the async model loading
    m_api->LoadModelFromSasAsync(options,
        // completed callback
        [this](RR::Status status, RR::ApiHandle<RR::LoadModelResult> result)
        {
            m_modelLoadResult = RR::StatusToResult(status);
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
        },
        // progress update callback
            [this](float progress)
        {
            // progress callback
            m_modelLoadingProgress = progress;
            m_needsStatusUpdate = true;
        });
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_sessionStartingTime = m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Connection();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>Покадровое обновление

Нам необходимо обновлять клиент по одному разу для каждого такта моделирования и выполнять ряд дополнительных операций обновления состояния. Функция `HolographicAppMain::Update` предоставляет хороший обработчик для покадровых обновлений.

#### <a name="state-machine-update"></a>Обновление конечного автомата

Нам необходимо выполнить опрос, чтобы проверить, перешел ли сеанс в состояние `Ready`. Если подключение установлено, можно начинать загрузку модели с помощью `StartModelLoading`.

Добавьте в тело функции `HolographicAppMain::Update` следующий код:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                m_session->GetPropertiesAsync([this](RR::Status status, RR::ApiHandle<RR::RenderingSessionPropertiesResult> propertiesResult)
                    {
                        if (status == RR::Status::OK)
                        {
                            auto ctx = propertiesResult->GetContext();
                            if (ctx.Result == RR::Result::Success)
                            {
                                auto res = propertiesResult->GetSessionProperties();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following ConnectAsync is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::RendererInitOptions init;
                                    init.IgnoreCertificateValidation = false;
                                    init.RenderMode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectAsync(init, [](RR::Status, RR::ConnectionStatus) {});
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                            }
                        }
                        else
                        {
                            SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                        }
                        m_sessionPropertiesQueryInProgress = false; // next try
                    });                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>Обновление системы координат

Нам необходимо согласовать систему координат с той, которая используется службой отрисовки. Для доступа к системе координат, которую необходимо использовать, требуется кадр `m_stationaryReferenceFrame`, созданный в конце функции `HolographicAppMain::OnHolographicDisplayIsAvailableChanged`.

Данная система координат обычно не изменяется, поэтому это однократная инициализация. Если же приложение изменит систему координат, ее необходимо вызвать снова.

Приведенный выше код задает систему координат в функции `Update` один раз и только при наличии эталонной системы координат и подключенного сеанса.

#### <a name="camera-update"></a>Обновление камеры

Нам необходимо обновить отсекающие плоскости камеры, чтобы обеспечить синхронизацию серверной и локальной камер. Это можно сделать в самом конце функции `Update`:

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>Отрисовка

Последнее, что нужно сделать, — это вызвать отрисовку удаленного содержимого. Нужно выполнить этот вызов на строго определенном участке конвейера отрисовки после очистки целевого объекта отрисовки и настройки окна просмотра. Вставьте следующий фрагмент кода в блокировку `UseHolographicCameraResources` внутри функции `HolographicAppMain::Render`:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Existing check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Запуск примера

Теперь пример должен находиться в таком состоянии, в котором его можно скомпилировать и выполнить.

Если пример запустится должным образом, перед вами появится вращающийся куб. После создания сеанса и загрузки модели он будет обрисовывать модель подсистемы, расположенную в текущей позиции заголовка. Создание сеанса и загрузка модели могут занять несколько минут. Текущее состояние записывается только на панель выходных данных Visual Studio. Вот почему рекомендуется запускать пример из Visual Studio.

> [!CAUTION]
> Клиент отключается от сервера, если функция отсчета тактов не вызывается в течение нескольких секунд. Поэтому активация точек останова может привести к отключению приложения.

Сведения о правильном отображении состояния на текстовой панели см. в предварительно подготовленной версии данного руководства на сайте GitHub.

## <a name="next-steps"></a>Дальнейшие действия

Из этого учебника вы узнали обо всех действиях, которые необходимо выполнить для добавления Удаленной отрисовки в базовый пример **приложения** C++/DirectX11.
Сведения о том, как преобразовать собственную модель, см в следующем кратком руководстве:

> [!div class="nextstepaction"]
> [Краткое руководство. Преобразование модели для отрисовки](../../../quickstarts/convert-model.md)