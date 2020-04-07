---
title: Настройка удаленного рендеринга для Unity
description: Как инициализировать удаленное рендерирование Azure в проекте Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681146"
---
# <a name="set-up-remote-rendering-for-unity"></a>Настройка удаленного рендеринга для Unity

Для обеспечения удаленного рендеринга Azure (ARR) в Unity мы предоставляем специальные методы, которые заботятся о некоторых аспектах Unity.

## <a name="startup-and-shutdown"></a>Запуск и выключение

Для инициализации `RemoteManagerUnity`удаленного рендеринга используйте . Этот класс вызывает `RemoteManager` в общий, но уже реализует для вас специфические сведения Unity. Например, Unity использует определенную систему координат. При `RemoteManagerUnity.Initialize`вызове будет создана соответствующая конвенция. Вызов также требует, чтобы вы предоставили камеру Unity, которая должна быть использована для отображения удаленно отображаемого содержимого.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Для выключения удаленного `RemoteManagerStatic.ShutdownRemoteRendering()`рендеринга звоните .

`AzureSession` После того, как был создан и выбран в качестве `RemoteManagerUnity`основного сеанса рендеринга, он должен быть зарегистрирован с:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Полный пример кода

Приведенный ниже код демонстрирует все шаги, необходимые для инициализации удаленного рендеринга Azure в Unity:

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

`RemoteManagerUnity.OnSessionUpdate`излучает события, когда состояние сеанса изменяется, см. документацию кода для деталей.

### <a name="arrserviceunity"></a>ARRServiceЕдинство

`ARRServiceUnity`является дополнительным компонентом для оптимизации настройки и управления сеансами. Он содержит параметры автоматического прекращения сеанса при выходе приложения или выходе из игрового режима в редакторе, а также автоматического продления аренды сеанса при необходимости. Он кэширует такие данные, `LastProperties` как свойства сеанса (см. его переменную) и предоставляет события для изменений состояния сеанса и ошибок сеанса.

Не может быть более одного `ARRServiceUnity` экземпляра за один раз. Это предназначено для быстрого запуска, реализуя некоторые общие функциональные возможности. Для большего применения это может быть предпочтительнее делать эти вещи самостоятельно, хотя.

Например, как настроить и `ARRServiceUnity` [использовать, см.](../../tutorials/unity/project-setup.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Установка пакета удаленного рендеринга для Unity](install-remote-rendering-unity-package.md)
* [Учебник: Настройка проекта Unity с нуля](../../tutorials/unity/project-setup.md)
