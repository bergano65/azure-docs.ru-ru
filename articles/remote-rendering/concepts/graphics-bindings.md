---
title: Графический переплет
description: Настройка графических привязок и случаев использования
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681887"
---
# <a name="graphics-binding"></a>Графический переплет

Чтобы использовать удаленное рендерирование Azure в пользовательском приложении, его необходимо интегрировать в конвейер визуализации приложения. Эта интеграция является ответственностью графической привязки.

После настройки, графический переплета дает доступ к различным функциям, которые влияют на отрисованные изображения. Эти функции можно разделить на две категории: общие функции, которые всегда `Microsoft.Azure.RemoteRendering.GraphicsApiType`доступны, и конкретные функции, которые имеют отношение только к выбранным.

## <a name="graphics-binding-in-unity"></a>Связывание графики в Unity

В Unity вся привязка `RemoteUnityClientInit` обрабатывается структурой, передаваемым в `RemoteManagerUnity.InitializeManager`. Чтобы установить графический `GraphicsApiType` режим, поле должно быть настроено на выбранную привязку. Поле будет автоматически заселено в зависимости от наличия XRDevice. Поведение может быть вручную перекрыто следующими поведениями:

* **HoloLens 2**: Всегда используется связывание графики [Windows Mixed Reality.](#windows-mixed-reality)
* **Плоский UWP настольное приложение:** [Моделирование](#simulation) всегда используется. Чтобы использовать этот режим, не забудьте следовать инструкциям в [учебном: Настройка проекта Unity с нуля.](../tutorials/unity/project-setup.md)
* **Редактор Unity**: [Моделирование](#simulation) всегда используется, если гарнитура WMR VR не подключена, и в этом случае ARR будет отключен, чтобы позволить отладить связанные с aRR части приложения. Смотрите также [голографические ремотивации](../how-tos/unity/holographic-remoting.md).

Единственной другой важной частью Unity является доступ к [базовой привязке,](#access)все остальные разделы ниже могут быть пропущены.

## <a name="graphics-binding-setup-in-custom-applications"></a>Настройка связывания графики в пользовательских приложениях

Чтобы выбрать привязку графики, сделайте следующие два шага: во-первых, при инициализации программы необходимо статически инициализированное:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

Приведенный выше вызов необходим для инициализации удаленного рендеринга Azure в голографические AIS. Эта функция должна быть вызвана до вызова голографического API и до того, как будут доступны какие-либо другие API удаленного рендеринга. Аналогичным образом, соответствующая функция `RemoteManagerStatic.ShutdownRemoteRendering();` де-инита должна быть вызвана после того, как больше не называются голографические AIS.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Доступ к графической привязке

После настройки клиента можно получить доступ к базовому `AzureSession.GraphicsBinding` связыванию графики с геттером. Например, последние статистические данные кадра можно получить следующим образом:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Графические AIS

Есть в настоящее время два графических `WmrD3D11` `SimD3D11`AIS, которые могут быть выбраны, и . Третий `Headless` существует, но еще не поддерживается на стороне клиента.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`является связующим связующим связующим значением по умолчанию для запуска на HoloLens 2. Это создаст `GraphicsBindingWmrD3d11` привязку. В этом режиме Azure Remote Rendering подключается непосредственно к голографическим AIS.

Чтобы получить доступ к производным графическим привязким, база `GraphicsBinding` должна быть отлита.
Есть две вещи, которые необходимо сделать, чтобы использовать привязку WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Информирование удаленного рендеринга используемой системы координат

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

В тех `ptr` случаях, когда вышеперечисленное должно быть указателем на родной `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` объект, определяющий мировую систему координат пространства, в которой выражены координаты в API.

#### <a name="render-remote-image"></a>Рендеринг удаленное изображение

В начале каждого кадра удаленный кадр должен быть отображен в задний буфер. Это делается `BlitRemoteFrame`путем вызова, который будет заполнить как цвет и глубина информации в настоящее время связаны рендера цели. Таким образом, важно, чтобы это было сделано после связывания задней буфера в качестве цели рендеринга.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Моделирование

`GraphicsApiType.SimD3D11`является привязкой моделирования и в `GraphicsBindingSimD3d11` случае выбора создает привязку графики. Этот интерфейс используется для имитации движения головы, например, в настольном приложении и отображает моноскопическое изображение.
Настройка немного более активное участие и работает следующим образом:

#### <a name="create-proxy-render-target"></a>Создание цели визуалии прокси

Удаленное и локальное содержимое должно быть отображено в заэкранный цвет / `GraphicsBindingSimD3d11.Update` глубину рендеринга цели называется "прокси" с использованием прокси-камеры данных, предоставляемых функцией. Прокси должен соответствовать разрешению задней буфера. Как только сеанс `GraphicsBindingSimD3d11.InitSimulation` готов, необходимо вызвать перед подключением к нему:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Функция init должна быть предоставлена указатели на родной d3d-устройство, а также на текстуру цвета и глубины прокси-цели рендеринга. `AzureSession.ConnectToRuntime` После инициализации, `DisconnectFromRuntime` и может быть вызван несколько `GraphicsBindingSimD3d11.DeinitSimulation` раз, но при переходе `GraphicsBindingSimD3d11.InitSimulation` на другой сессии, необходимо сначала на старой сессии, прежде чем может быть вызван на другой сессии.

#### <a name="render-loop-update"></a>Обновление цикла рендера

Обновление цикла рендера состоит из нескольких этапов:

1. Каждый кадр, прежде чем `GraphicsBindingSimD3d11.Update` любой рендеринг происходит, вызывается с текущей преобразования камеры, которая передается на сервер для визуализации. В то же время возвращенное преобразование прокси должно быть применено к прокси-камере для визуализации в цель визуалии прокси-
Если возвращенное `SimulationUpdate.frameId` обновление прокси является нулевым, удаленных данных пока нет. В этом случае вместо отображения в цель визы прокси-содержимого любое локальное содержимое должно быть отображено в задней буфер непосредственно с использованием текущих данных камеры, и следующие два шага пропущены.
1. Приложение должно теперь связывать цель `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`визуалистии прокси и вызов. Это позволит заполнить удаленный цвет и глубину информации в прокси-рендера цели. Любое локальное содержимое теперь может быть отображено на прокси-сервере с помощью преобразования прокси-камеры.
1. Далее, задний буфер должен быть связан `GraphicsBindingSimD3d11.ReprojectProxy` в качестве цели рендеринга и вызван в какой момент задний буфер может быть представлен.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник: Настройка проекта Unity с нуля](../tutorials/unity/project-setup.md)
