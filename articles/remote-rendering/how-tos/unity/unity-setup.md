---
title: Настройка Удаленной отрисовки для Unity
description: Как инициализировать удаленную визуализацию Azure в проекте Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: f3400d82a6aa184daabfa2ebbe6b775b8e4c1562
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565462"
---
# <a name="set-up-remote-rendering-for-unity"></a>Настройка Удаленной отрисовки для Unity

Чтобы включить удаленную визуализацию Azure (ARR) в Unity, мы предоставляем выделенные методы, которые отвечают определенным аспектам Unity.

## <a name="startup-and-shutdown"></a>Запуск и завершение работы

Чтобы инициализировать удаленную визуализацию, используйте `RemoteManagerUnity` . Этот класс вызывает универсальный интерфейс, `RemoteManager` но уже реализует сведения, относящиеся к Unity. Например, Unity использует определенную систему координат. При вызове `RemoteManagerUnity.Initialize` будет настроено правильное соглашение. Кроме того, вызов требует предоставления камеры Unity, которая должна использоваться для отображения содержимого, отображаемого удаленно.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Для выключения удаленной отрисовки вызовите `RemoteManagerStatic.ShutdownRemoteRendering()` .

После `AzureSession` создания и выбора в качестве основного сеанса визуализации он должен быть зарегистрирован в `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Полный пример кода

В приведенном ниже коде показаны все шаги, необходимые для инициализации удаленной отрисовки Azure в Unity.

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Удобные функции

### <a name="session-state-events"></a>События состояния сеанса

`RemoteManagerUnity.OnSessionUpdate`выдает события при изменении состояния сеанса. подробные сведения см. в документации по коду.

### <a name="arrserviceunity"></a>аррсервицеунити

`ARRServiceUnity`— Это необязательный компонент для упрощения установки и управления сеансами. Он содержит параметры для автоматической прекращения сеанса при выходе или выходе из режима воспроизведения приложения, а также при необходимости автоматически обновлять аренду сеанса. Он кэширует такие данные, как свойства сеанса (см. его `LastProperties` переменную), и предоставляет события для изменений состояния сеанса и ошибок сеанса.

В каждый момент времени не может быть более одного экземпляра `ARRServiceUnity` . Он предназначен для быстрого начала работы путем реализации некоторых распространенных функциональных возможностей. Однако для более крупного приложения может быть предпочтительным выполнить эти действия самостоятельно.

Пример настройки и использования `ARRServiceUnity` см. в разделе Обзор [моделей с удаленным](../../tutorials/unity/view-remote-models/view-remote-models.md)просмотром.

## <a name="next-steps"></a>Дальнейшие шаги

* [Установка пакета Удаленной отрисовки для Unity](install-remote-rendering-unity-package.md)
* [Учебник. Просмотр удаленных моделей](../../tutorials/unity/view-remote-models/view-remote-models.md)
