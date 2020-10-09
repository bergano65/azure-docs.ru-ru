---
title: Добавление библиотеки Azure Kinect в проект Visual Studio
description: Узнайте, как добавить пакет NuGet для Azure Kinect в проект Visual Studio.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, датчик, пакет SDK, Visual Studio 2017, Visual Studio 2019, NuGet
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276971"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Добавление библиотеки Azure Kinect в проект Visual Studio

В этой статье описывается процесс добавления пакета NuGet для Azure Kinect в проект Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Установка пакета NuGet для Azure Kinect

Чтобы установить пакет NuGet для Azure Kinect, выполните следующие действия.

1. Подробные инструкции по установке пакета NuGet в Visual Studio см. в [кратком руководстве по установке и использованию пакета в Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Чтобы добавить пакет, можно использовать пользовательский интерфейс диспетчера пакетов, щелкнув правой кнопкой мыши ссылки и выбрав пункт Управление пакетами NuGet из обозреватель решений.
3. Выберите [NuGet.org](https://www.nuget.org) в качестве источника пакета, выберите вкладку Обзор и выполните поиск по запросу `Microsoft.Azure.Kinect.Sensor` .
4. Выберите этот пакет в списке и установите.

## <a name="use-azure-kinect-nuget-package"></a>Использование пакета NuGet для Azure Kinect

После добавления пакета добавьте файл заголовка в исходный код, например:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>[Теперь вы готовы к созданию первого приложения](build-first-app.md)
