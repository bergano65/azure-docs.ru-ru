---
title: Оптимизация Visual Studio для Service Fabric сети Azure
description: В этой статье показано, как оптимизировать производительность Visual Studio для проектов Сетки Service Fabric, чтобы первый запуск отладки (F5) был намного быстрее.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497975"
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