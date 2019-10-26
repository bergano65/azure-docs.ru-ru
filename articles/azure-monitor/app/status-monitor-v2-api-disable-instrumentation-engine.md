---
title: Справочник по API агента Azure Application Insights
description: Справочник по API агента Application Insights. Disable-Инструментатионенгине. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dd546107164632c821c1ee9a1629fe0a1ca071fc
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899760"
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
