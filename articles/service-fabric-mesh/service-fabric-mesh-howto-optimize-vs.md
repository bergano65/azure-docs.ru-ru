---
title: Оптимизация производительности Visual Studio для проектов Сетки Azure Service Fabric | Документация Майкрософт
description: В этой статье показано, как оптимизировать производительность Visual Studio для проектов Сетки Service Fabric, чтобы первый запуск отладки (F5) был намного быстрее.
services: service-fabric-mesh
keywords: оптимизация производительности отладки
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 04aeaa6b008b50789f4380e4bb98beba3957c2e9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663433"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Оптимизация производительности Visual Studio для проектов Сетки Service Fabric

В этой статье показано, как оптимизировать производительность Visual Studio для проектов Сетки Service Fabric, чтобы первый запуск отладки (F5) был намного быстрее.  

## <a name="change-visual-studio-settings"></a>Изменение настроек Visual Studio
 
В Visual Studio в разделе **Средства** > **Параметры**  > **Средства Сетки Service Fabric** > **Общие** можно настроить следующие параметры:

- **Извлечение необходимых образов Docker при открытии проекта** ускоряет ваш первый запуск отладки (F5), запуская процесс загрузки образа во время загрузки проекта.  
- **Развертывание приложения при открытом проекте** может ускорить ваш первый запуск отладки (F5), запустив процесс развертывания после открытия проекта.  
- **Удаление приложения при закрытии проекта** восстанавливает ресурсы (ЦП, ОЗУ), выделенные приложению, удаляя приложение Сетки при закрытии проекта.  

При появлении сообщения в окне выходных данных средства Service Fabric, когда Visual Studio "извлекает образы", "прогревает" или "удаляет приложения", это относится к настройкам выше. Эти параметры можно отключить.

## <a name="next-steps"></a>Дальнейшие действия

Прочтите [Руководство приложения по отладке Сетки](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)