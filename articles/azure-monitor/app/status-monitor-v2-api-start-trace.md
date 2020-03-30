---
title: Ссылка на API агентства API для анализа приложений приложений Azure
description: Ссылка на API агента приложений. Стартовый след. Сбор журналов ETW из Status Monitor и SDK Insight.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671228"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Приложение Insights Агент API: Старт-ApplicationInsightsMonitoringTrace

В этой статье описывается cmdlet, который является членом [модуля Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Собирает [события ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) из безкодатого времени выполнения. Этот cmdlet является альтернативой запуску [PerfView](https://github.com/microsoft/perfview).

Собранные события будут распечатаны на консоли в режиме реального времени и сохранены в файле ETL. Выводный файл ETL может быть открыт [PerfView](https://github.com/microsoft/perfview) для дальнейшего изучения.

Этот cmdlet будет работать до тех пор, пока не достигнет длительности тайм-аута (по умолчанию 5 минут) или остановлен вручную ().`Ctrl + C`

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin.

## <a name="examples"></a>Примеры

### <a name="how-to-collect-events"></a>Как собирать события

Обычно мы просим вас собирать события для расследования того, почему ваше приложение не является инструментальным.

Безкодное время выполнения прикрепите будет излучать события ETW при запуске IIS и при запуске приложения.

Для сбора этих событий:
1. В cmd консоли с привилегиями админа `iisreset /stop` выполните, чтобы отключить IIS и все веб-приложения.
2. Выполнить этот cmdlet
3. В cmd консоли с привилегиями админа `iisreset /start` выполните, чтобы начать IIS.
4. Попробуйте просмотреть в приложении.
5. После завершения загрузки приложения можно вручную`Ctrl + C`остановить его () или дождаться тайм-аута.

### <a name="what-events-to-collect"></a>Какие события собирать

При сборе событий у вас есть три варианта:
1. Используйте `-CollectSdkEvents` переключатель для сбора событий, испускаемых sDK Application Insights.
2. Используйте `-CollectRedfieldEvents` переключатель для сбора событий, испускаемых Status Monitor и Redfield Runtime. Эти журналы полезны при диагностике IIS и запуска приложений.
3. Используйте оба коммутатора для сбора обоих типов событий.
4. По умолчанию, если коммутатор не указан, оба типа событий будут собраны.


## <a name="parameters"></a>Параметры

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Дополнительные.** Используйте этот параметр, чтобы установить, как долго этот скрипт должен собирать события. Значение по умолчанию — 5 минут.

### <a name="-logdirectory"></a>-ЛогДиректори
**Дополнительные.** Используйте этот переключатель для настройки каталога вывода файла ETL. По умолчанию этот файл будет создан в каталоге модулей PowerShell. Полный путь будет отображаться во время выполнения сценария.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Дополнительные.** Используйте этот переключатель для сбора событий Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Дополнительные.** Используйте этот переключатель для сбора событий из Status Monitor и времени выполнения Redfield.

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для вывода подробные журналы.



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

Дополнительное устранение неполадок:

- Просмотрите дополнительные шаги по устранению неполадок здесь:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Просмотрите [ссылку на API,](status-monitor-v2-overview.md#powershell-api-reference) чтобы узнать о параметрах, которые вы могли пропустить.
- Если вам нужна дополнительная помощь, вы можете связаться с нами на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Сделайте больше с агентом По анализу приложений:
 - Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
 - [Получите конфигурацию,](status-monitor-v2-api-get-config.md) чтобы подтвердить, что ваши настройки были записаны правильно.
 - [Получите статус](status-monitor-v2-api-get-status.md) для проверки мониторинга.
