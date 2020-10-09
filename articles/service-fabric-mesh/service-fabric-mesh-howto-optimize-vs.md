---
title: Оптимизация Visual Studio для Service Fabric сети Azure
description: В этой статье показано, как оптимизировать производительность Visual Studio для проектов Сетки Service Fabric, чтобы первый запуск отладки (F5) был намного быстрее.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: aa7a959128d3bcdfcce67d3abeac245975339a9f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840308"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Оптимизация производительности Visual Studio для проектов Сетки Service Fabric

В этой статье показано, как оптимизировать производительность Visual Studio для проектов Сетки Service Fabric, чтобы первый запуск отладки (F5) был намного быстрее.  

## <a name="change-visual-studio-settings"></a>Изменение настроек Visual Studio
 
В Visual Studio в разделе **Сервис**  >  **Параметры**   >  **Service Fabric средства сетки**  >  **Общие**можно настроить следующие параметры.

- **Извлечение необходимых образов Docker при открытии проекта** ускоряет ваш первый запуск отладки (F5), запуская процесс загрузки образа во время загрузки проекта.  
- **Развертывание приложения при открытом проекте** может ускорить ваш первый запуск отладки (F5), запустив процесс развертывания после открытия проекта.  
- **Удаление приложения при закрытии проекта** восстанавливает ресурсы (ЦП, ОЗУ), выделенные приложению, удаляя приложение Сетки при закрытии проекта.  

При появлении сообщения в окне выходных данных средства Service Fabric, когда Visual Studio "извлекает образы", "прогревает" или "удаляет приложения", это относится к настройкам выше. Эти параметры можно отключить.

## <a name="next-steps"></a>Дальнейшие действия

Прочтите [Руководство приложения по отладке Сетки](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)