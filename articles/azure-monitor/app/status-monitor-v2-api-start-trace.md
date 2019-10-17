---
title: 'Справочник по API агента Application Insights Azure: запуск трассировки | Документация Майкрософт'
description: Справочник по API агента Application Insights. Start-Trace. Собирайте журналы ETW из монитор состояния и Application Insights SDK.
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
ms.openlocfilehash: b1c5aa34c46a20631b328abfb061dc2477150c72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389852"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>API агента Application Insights: Start-Аппликатионинсигхтсмониторингтраце

В этой статье описывается командлет, который является членом [модуля PowerShell AZ. аппликатионмонитор](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Собирает [события ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) из среды выполнения с некодированным подключением. Этот командлет является альтернативой запуску [PerfView](https://github.com/microsoft/perfview).

Собранные события будут выводиться на консоль в режиме реального времени и сохранены в ETL-файл. Выходной ETL-файл можно открыть с помощью [PerfView](https://github.com/microsoft/perfview) для дальнейшего изучения.

Этот командлет будет выполняться до тех пор, пока не достигнет времени ожидания (по умолчанию 5 минут) или остановлен вручную (`Ctrl + C`).

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

## <a name="examples"></a>Примеры

### <a name="how-to-collect-events"></a>Получение событий

Обычно мы попросим вас собраться с событиями, чтобы выяснить, почему приложение не оснащено.

Среда выполнения присоединения, не имеющая кода, будет создавать события ETW при запуске IIS и при запуске приложения.

Чтобы получить эти события, сделайте следующее:
1. В консоли CMD с правами администратора выполните `iisreset /stop`, чтобы отключить службы IIS и все веб-приложения.
2. Выполните этот командлет
3. В консоли CMD с правами администратора выполните `iisreset /start`, чтобы запустить службы IIS.
4. Попробуйте перейти к приложению.
5. После завершения загрузки приложения можно вручную его отключить (`Ctrl + C`) или подождать истечения времени ожидания.

### <a name="what-events-to-collect"></a>События для собраний

При сборе событий можно использовать три варианта:
1. Используйте параметр `-CollectSdkEvents` для получения событий, порожденных из пакета SDK для Application Insights.
2. Используйте параметр `-CollectRedfieldEvents`, чтобы получить события, выдаваемые монитор состояния и средой выполнения Redfield. Эти журналы полезны при диагностике служб IIS и запуска приложений.
3. Используйте оба параметра для накопления обоих типов событий.
4. По умолчанию, если параметр не указан, будут собираться оба типа событий.


## <a name="parameters"></a>Параметры

### <a name="-maxdurationinminutes"></a>-Максдуратионинминутес
**Необязательный параметр.** Используйте этот параметр, чтобы задать время, в течение которого этот скрипт должен получать события. Значение по умолчанию — 5 минут.

### <a name="-logdirectory"></a>-LogDirectory
**Необязательный параметр.** Используйте этот параметр, чтобы задать выходной каталог ETL-файла. По умолчанию этот файл будет создан в каталоге модулей PowerShell. Полный путь будет отображаться во время выполнения скрипта.


### <a name="-collectsdkevents"></a>-Коллектсдкевентс
**Необязательный параметр.** Используйте этот параметр для получения Application Insights событий пакета SDK.

### <a name="-collectredfieldevents"></a>-Коллектредфиелдевентс
**Необязательный параметр.** Используйте этот параметр для получения событий из монитор состояния и среды выполнения Redfield.

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот параметр для вывода подробных журналов.



## <a name="output"></a>Выходные данные


### <a name="example-of-application-startup-logs"></a>Пример журналов запуска приложений
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные способы устранения неполадок:

- Дополнительные шаги по устранению неполадок приведены здесь: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Ознакомьтесь со [справочником по API](status-monitor-v2-overview.md#powershell-api-reference) , чтобы узнать о параметрах, которые могли быть пропущены.
- Если вам нужна дополнительная помощь, вы можете связаться с нами на сайте [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Другие действия с агентом Application Insights:
 - Используйте наше справочное по для [устранения неполадок](status-monitor-v2-troubleshoot.md) агента Application Insights.
 - [Получите конфигурацию](status-monitor-v2-api-get-config.md) , чтобы убедиться, что параметры записаны правильно.
 - [Получение состояния](status-monitor-v2-api-get-status.md) для проверки мониторинга.
