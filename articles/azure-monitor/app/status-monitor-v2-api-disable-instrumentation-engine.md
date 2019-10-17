---
title: 'Справочник по API агента Azure Application Insights: Отключение модуля инструментирования | Документация Майкрософт'
description: Справочник по API агента Application Insights. Disable-Инструментатионенгине. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: aaf493b5f2075ead62087824773f21643a1246e4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388311"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>API агента Application Insights: Disable-Инструментатионенгине

В этой статье описывается командлет, который является членом [модуля PowerShell AZ. аппликатионмонитор](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание
Отключает модуль инструментирования, удаляя некоторые разделы реестра.
Перезапустите IIS, чтобы изменения вступили в силу.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

## <a name="examples"></a>Примеры

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Параметры 

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот параметр для вывода подробных журналов.

## <a name="output"></a>Выходные данные


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Пример выходных данных для успешного отключения модуля инструментирования

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Дальнейшие действия

 Другие действия с агентом Application Insights:
 - Используйте наше справочное по для [устранения неполадок](status-monitor-v2-troubleshoot.md) агента Application Insights.
