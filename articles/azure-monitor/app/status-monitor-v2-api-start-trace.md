---
title: 'Монитор состояния v2 Справочник по API Azure: Запустите трассировку | Документация Майкрософт'
description: Справочник по API v2 монитор состояния. Трассировки Start. Собирать журналы трассировки событий Windows из монитора состояния и пакет SDK Application Insights.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 186324e6a93f90fd04323074e5dc883e3e271725
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478996"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v031-alpha"></a>Состояние монитора v2 API: Start-ApplicationInsightsMonitoringTrace (v0.3.1-альфа-версия)

В этой статье описывается командлет, который является членом [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Описание

Собирает [события трассировки событий Windows](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) от предоставления без помощи кода присоединение среды выполнения. Этот командлет можно использовать вместо выполнения [PerfView](https://github.com/microsoft/perfview).

Собранные события выводится на консоль в режиме реального времени и сохраняется в ETL-файл. Выходной файл ETL можно открыть [PerfView](https://github.com/microsoft/perfview) для дальнейшего изучения.

Этот командлет будет выполняться, пока достигнет время ожидания (по умолчанию 5 минут) или остановлена вручную (`Ctrl + C`).

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

## <a name="examples"></a>Примеры

### <a name="how-to-collect-events"></a>Сбор событий

Обычно мы хотели бы попросить собрать событиями для выявления, почему не инструментирования приложения.

Предоставления без помощи кода присоединение среды выполнения будет выдавать события трассировки событий Windows, при запуске IIS, и при запуске приложения.

Для сбора этих событий:
1. В консоли команд с правами администратора, выполните `iisreset /stop` отключение IIS и всех веб-приложений.
2. Выполните этот командлет
3. В консоли команд с правами администратора, выполните `iisreset /start` запуск сервера IIS.
4. Попробуйте перейти к своему приложению.
5. После завершения загрузки приложения его можно вручную остановить (`Ctrl + C`) или подождать, пока время ожидания.

### <a name="what-events-to-collect"></a>События для сбора

При сборе событий есть три варианта:
1. Используйте параметр `-CollectSdkEvents` для сбора события, переданные из пакета SDK Application Insights.
2. Используйте параметр `-CollectRedfieldEvents` для сбора события, генерируемые монитор состояния и Redfield средой выполнения. Эти журналы могут быть полезны при диагностике IIS и запуска приложения.
3. Используйте оба параметра для сбора обоих типов событий.
4. По умолчанию, если переключатель не указан, что оба типа событий будут собираться.


## <a name="parameters"></a>Параметры

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Необязательный параметр.** Используйте этот параметр, чтобы задать, как долго этот скрипт следует собирать события. Значение по умолчанию — 5 минут.

### <a name="-logdirectory"></a>-LogDirectory
**Необязательный параметр.** Используйте этот параметр, чтобы задать в выходной каталог ETL-файла. По умолчанию этот файл создается в каталоге модулей PowerShell. Полный путь будет отображаться во время выполнения скрипта.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Необязательный параметр.** Этот параметр можно используйте для сбора событий пакета SDK Application Insights.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Необязательный параметр.** Этот параметр можно используйте для сбора событий монитор состояния и Redfield среды выполнения.

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Этот параметр можно используйте для вывода подробных журналов.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>Примеры журналов запуска приложения
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

Дополнительные настройки:

- Просмотрите дополнительные действия по устранению ниже: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Просмотрите [Справочник по API](status-monitor-v2-overview.md#powershell-api-reference) Дополнительные сведения о параметрах, вы не решены.
- Если вам нужна дополнительная помощь, вы можете обратиться к нам на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Новые возможности в версии 2 монитор состояния:
 - Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
 - [Получить конфигурацию](status-monitor-v2-api-get-config.md) для подтверждения того, что параметры были записаны неправильно.
 - [Получить состояние](status-monitor-v2-api-get-status.md) для проверки наблюдения.
