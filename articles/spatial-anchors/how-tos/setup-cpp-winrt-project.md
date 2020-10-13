---
title: Установка пространственных привязок Azure для приложения C++/WinRT HoloLens
description: Настройка проекта/WinRT HoloLens C++ для использования пространственных привязок Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 710f023a64f66e486bb2db9c08807af3431d0016
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87812340"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Настройка пространственных привязок Azure в проекте C++/WinRT HoloLens

## <a name="requirements"></a>Требования

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) устанавливается вместе с рабочей нагрузкой **универсальная платформа Windows Development** и **Windows 10 SDK (10.0.18362.0 или более поздней версии)** .

## <a name="configuring-a-project"></a>Настройка проекта

Пространственные привязки Azure для HoloLens и C++/WinRT распределяются с помощью пакета NuGet [Microsoft. Azure. спатиаланчорс. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) .

Следуйте приведенным [здесь](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio) инструкциям, чтобы использовать диспетчер пакетов NuGet Visual Studio для установки пакета NuGet [Microsoft. Azure. спатиаланчорс. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) в проект.

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Как создавать и размещать привязки в C++/WinRT](./create-locate-anchors-cpp-winrt.md)
