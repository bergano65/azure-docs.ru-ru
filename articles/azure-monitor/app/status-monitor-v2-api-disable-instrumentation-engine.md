---
title: Azure Application Insights Agent отстечивать-инструментированиеEngine
description: Ссылка на API агента приложений. Отмашина-ИнструментацияEngine. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998380"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Приложение Insights Agent API: Отключить-ИнструментацияEngine

В этой статье описывается cmdlet, который является членом [модуля Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание
Отключает приборный механизм, удаляя некоторые ключи реестра.
Перезапустите IIS для вхотворемых изменений.

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin.

## <a name="examples"></a>Примеры

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Параметры 

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для вывода подробные журналы.

## <a name="output"></a>Выходные данные


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Пример выхода от успешного отключения приборного двигателя

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Следующие шаги

 Сделайте больше с агентом По анализу приложений:
 - Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
