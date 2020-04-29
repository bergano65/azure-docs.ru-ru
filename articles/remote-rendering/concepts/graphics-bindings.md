---
title: Графическая привязка
description: Настройка графических привязок и вариантов использования
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681887"
---
# <a name="graphics-binding"></a>Графическая привязка

Чтобы иметь возможность использовать удаленную визуализацию Azure в пользовательском приложении, ее необходимо интегрировать в конвейер отрисовки приложения. Эта интеграция является обязанностью графической привязки.

После настройки графическая привязка предоставляет доступ к различным функциям, влияющим на визуализированный образ. Эти функции можно разделить на две категории: общие функции, которые всегда доступны, и конкретные функции, которые относятся только к выбранному `Microsoft.Azure.RemoteRendering.GraphicsApiType`.

## <a name="graphics-binding-in-unity"></a>Графическая привязка в Unity

В Unity вся привязка обрабатывается `RemoteUnityClientInit` структурой, передаваемой в. `RemoteManagerUnity.InitializeManager` Чтобы задать графический режим, `GraphicsApiType` поле должно быть установлено в выбранную привязку. Поле будет заполнено автоматически в зависимости от наличия Ксрдевице. Поведение можно переопределить вручную, используя следующие варианты поведения:

* **HoloLens 2**: всегда используется привязка графики [Windows Mixed Reality](#windows-mixed-reality) .
* **Плоское классическое приложение UWP**: [имитация](#simulation) всегда используется. Чтобы использовать этот режим, выполните действия, описанные в разделе [учебник. Настройка проекта Unity с нуля](../tutorials/unity/project-setup.md).
* **Редактор Unity**. [Эмуляция](#simulation) всегда используется, пока не будет ПОДКЛЮЧЕНА гарнитура ВМР VR, в случае чего параметр arr будет отключен, чтобы разрешить отладку частей приложения, не связанных с arr. См. также с [holographic удаленное взаимодействие](../how-tos/unity/holographic-remoting.md).

Единственная другая соответствующая часть для Unity — доступ к [базовой привязке](#access), все остальные разделы, приведенные ниже, могут быть пропущены.

## <a name="graphics-binding-setup-in-custom-applications"></a>Настройка привязки графики в пользовательских приложениях

Чтобы выбрать графическую привязку, сделайте следующее: во-первых, при инициализации программы должна быть инициализирована статическая привязка графики.

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

Приведенный выше вызов необходим для инициализации удаленной отрисовки Azure в более holographic API. Эта функция должна вызываться до вызова любого из holographic API и до обращения к другим интерфейсам API удаленной подготовки к просмотру. Аналогичным образом, соответствующая функция `RemoteManagerStatic.ShutdownRemoteRendering();` de-init должна вызываться после того, как более holographic-интерфейсы больше не вызываются.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Доступ к графической привязке

После настройки клиента можно получить доступ к базовой графической привязке с помощью `AzureSession.GraphicsBinding` метода получения. В качестве примера можно получить последнюю статистику кадра следующим образом:

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

## <a name="graphic-apis"></a>Графические API

В настоящее время существует два графических API, которые можно выбрать `WmrD3D11` , `SimD3D11`и. Третий элемент `Headless` существует, но еще не поддерживается на стороне клиента.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`Привязка по умолчанию для запуска в HoloLens 2. Будет создана `GraphicsBindingWmrD3d11` привязка. В этом режиме Удаленная подготовка к просмотру Azure переключается непосредственно в более holographic API.

Для доступа к производным графическим привязкам основание `GraphicsBinding` должно быть приведено к типу.
Для использования привязки ВМР необходимо выполнить два действия:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Уведомление об удаленной отрисовке используемой системы координат

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Где выше `ptr` должен быть указателем на собственный `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` объект, определяющий систему координат в мировом пространстве, в которой координаты в API выражаются в.

#### <a name="render-remote-image"></a>Отобразить удаленный образ

В начале каждого кадра Удаленная рамка должна быть подготовлена к просмотру в обратном буфере. Это делается путем вызова метода `BlitRemoteFrame`, который будет заполнять сведения о цвете и глубине в привязанном к текущей цели отрисовки. Поэтому важно, чтобы это произошло после привязки заднего буфера в качестве целевого объекта отрисовки.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Моделирование

`GraphicsApiType.SimD3D11`является привязкой моделирования и, если она выбрана `GraphicsBindingSimD3d11` , создает графическую привязку. Этот интерфейс используется для имитации головного движения, например в классическом приложении, и для отрисовки однообразного изображения.
Установка является более сложной и работает следующим образом.

#### <a name="create-proxy-render-target"></a>Создание целевого объекта отрисовки прокси-сервера

Удаленное и локальное содержимое должно быть подготовлено к просмотру с помощью целевого объекта отрисовки цвета или глубины с именем "прокси" с `GraphicsBindingSimD3d11.Update` использованием данных камеры прокси, предоставленных функцией. Прокси-сервер должен соответствовать разрешению заднего буфера. После того как сеанс будет готов `GraphicsBindingSimD3d11.InitSimulation` , его необходимо вызвать перед подключением к нему:

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

Функция init должна быть предоставлена с указателями на собственное устройство D3D, а также к цвету и текстуре глубины целевого объекта отрисовки прокси-сервера. После инициализации `AzureSession.ConnectToRuntime` и `DisconnectFromRuntime` может вызываться несколько раз, но при переключении на другой сеанс необходимо сначала `GraphicsBindingSimD3d11.DeinitSimulation` вызвать метод в старом сеансе, прежде чем `GraphicsBindingSimD3d11.InitSimulation` его можно будет вызвать в другом сеансе.

#### <a name="render-loop-update"></a>Обновление цикла подготовки к просмотру

Обновление цикла отрисовки состоит из нескольких шагов:

1. Каждый кадр, перед отрисовкой, `GraphicsBindingSimD3d11.Update` вызывается с текущим преобразованием камеры, переданным на сервер для подготовки к просмотру. В то же время для подготовки к просмотру целевого объекта отрисовки прокси-сервера необходимо применить преобразованное прокси-сервер.
Если возвращенное обновление `SimulationUpdate.frameId` прокси-сервера имеет значение null, удаленные данные пока отсутствуют. В этом случае вместо отрисовки в целевом объекте прорисовки прокси-сервера любое локальное содержимое должно быть визуализировано в обратном буфере непосредственно с использованием текущих данных камеры, а следующие два шага пропускаются.
1. Теперь приложение должно выполнить привязку целевого объекта отрисовки и `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`вызова. Это приведет к заполнению удаленного цвета и сведений о глубине на целевой объект отрисовки прокси-сервера. Теперь любое локальное содержимое можно отобразить на прокси-сервере, используя преобразование с помощью прокси-камеры.
1. Затем задний буфер должен быть привязан в качестве целевого объекта рендеринга и `GraphicsBindingSimD3d11.ReprojectProxy` вызываться, после чего можно представить задний буфер.

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

## <a name="next-steps"></a>Дальнейшие шаги

* [Руководство. Настройка проекта Unity с нуля](../tutorials/unity/project-setup.md)
