---
title: Оптимизация Visual Studio для Service Fabric сети Azure
description: В этой статье показано, как оптимизировать производительность Visual Studio для проектов Сетки Service Fabric, чтобы первый запуск отладки (F5) был намного быстрее.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625758"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Оптимизация производительности Visual Studio для проектов Сетки Service Fabric

> [!IMPORTANT]
> Предварительная версия сетки Service Fabric Azure была снята с учета. Новые развертывания больше не будут разрешены через интерфейс API Service Fabricной сетки. Поддержка существующих развертываний будет продолжена 28 апреля 2021 г.
> 
> Дополнительные сведения см. в статье о прекращении использования [предварительной версии сети Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

В этой статье показано, как оптимизировать производительность Visual Studio для проектов Сетки Service Fabric, чтобы первый запуск отладки (F5) был намного быстрее.  

## <a name="change-visual-studio-settings"></a>Изменение настроек Visual Studio
 
В Visual Studio в разделе **Сервис**  >  **Параметры**   >  **Service Fabric средства сетки**  >  **Общие** можно настроить следующие параметры.

- **Извлечение необходимых образов Docker при открытии проекта** ускоряет ваш первый запуск отладки (F5), запуская процесс загрузки образа во время загрузки проекта.  
- **Развертывание приложения при открытом проекте** может ускорить ваш первый запуск отладки (F5), запустив процесс развертывания после открытия проекта.  
- **Удаление приложения при закрытии проекта** восстанавливает ресурсы (ЦП, ОЗУ), выделенные приложению, удаляя приложение Сетки при закрытии проекта.  

При появлении сообщения в окне выходных данных средства Service Fabric, когда Visual Studio "извлекает образы", "прогревает" или "удаляет приложения", это относится к настройкам выше. Эти параметры можно отключить.

## <a name="next-steps"></a>Дальнейшие действия

Прочтите [Руководство приложения по отладке Сетки](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)