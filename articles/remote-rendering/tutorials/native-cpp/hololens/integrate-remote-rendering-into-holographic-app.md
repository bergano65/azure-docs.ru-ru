---
title: Интеграция Удаленной отрисовки в голографическое приложение C++/DirectX11
description: Объяснение процесса интеграции Удаленной отрисовки в простое голографическое приложение C++/DirectX11, созданное с помощью мастера проектов Visual Studio
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 9db32912e86079875ad382fb23e521c720fc7fbd
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775622"
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

* Данные учетной записи (идентификатор и ключ учетной записи, идентификатор подписки). Если у вас нет учетной записи, [создайте ее](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(скачать)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* Последняя версия Visual Studio 2019 [(скачать)](https://visualstudio.microsoft.com/vs/older-downloads/).
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

Добавьте также эту дополнительную директиву `include` в файл HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

Для простоты кода мы определяем следующий ярлык пространства имен в начале файла HolographicAppMain.h после директивы `include`:

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
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Для фактической реализации отлично подойдет конструктор класса `HolographicAppMain`. В этом случае необходимо выполнить инициализацию трех типов:
1. однократную инициализацию системы Удаленной отрисовки;
1. создание внешнего интерфейса;
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
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
В локальной функции `SetNewSession` выполняется регистрация обратного вызова, который активируется при каждом изменении состояния подключения для данного сеанса. Мы перенаправляем этот вызов в собственную функцию `OnConnectionStatusChanged`. Мы объявим и реализуем его (а также остальную часть кода конечного автомата) в следующем абзаце. Также обратите внимание, что в примере жестко закодированы учетные данные, поэтому их необходимо подставить ([идентификатор учетной записи, ключ учетной записи](../../../how-tos/create-an-account.md#retrieve-the-account-information) и [домен](../../../reference/regions.md)).

Инициализацию мы отменяем симметрично и делаем это в обратном порядке в конце тела деструктора.

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Конечный автомат

В Удаленной отрисовке асинхронные функции являются ключевыми для создания сеанса и загрузки модели. Чтобы это учесть, нам нужен простой конечный автомат, который, по сути, автоматически переходит между следующими состояниями:

*Инициализация —> создание сеанса —> модуль загрузки (с ходом выполнения)*

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
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

На стороне реализации в CPP-файле добавьте следующие тела функций:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
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

Для клиента необходимо отсчитывать по одному такту на каждый такт моделирования. Класс `HolographicApp1Main` предоставляет хороший обработчик для покадровых обновлений, поэтому добавьте следующий код в тело функции `HolographicApp1Main::Update`:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>Отрисовка

Последнее, что нужно сделать, — это вызвать отрисовку удаленного содержимого. Мы должны выполнить этот вызов в строго определенном месте конвейера отрисовки после очистки целевого объекта отрисовки. Вставьте следующий фрагмент кода в блокировку `UseHolographicCameraResources` внутри функции `HolographicAppMain::Render`:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
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
