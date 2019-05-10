---
title: Монитор состояния v2 Устранение неполадок Azure и известных проблем | Документация Майкрософт
description: Известные проблемы в версии 2 монитор состояния и устранения неполадок примеры. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: d640206fd72b4eb89afe5ed1750627823bca9637
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415881"
---
# <a name="troubleshooting-status-monitor-v2"></a>Устранение неполадок с состоянием отслеживать версии 2

При включении мониторинга, могут возникнуть проблемы, препятствующие сбора данных. В этом документе перечислены все известные проблемы и устранения неполадок примеры.
Если вы столкнетесь с проблемой, не перечисленные здесь, можно связаться с нами [здесь](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>Известные проблемы

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>Конфликтующие библиотек DLL в каталог bin приложения

Если любой из этих библиотек DLL в каталоге bin, мониторинга может завершиться ошибкой.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Некоторые из этих библиотек DLL включены в шаблоны приложений по умолчанию Visual Studio, даже если приложение не использует их.
Поведение признаков можно увидеть при помощи средства устранения неполадок:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- iisreset + приложение загрузить (NO ТЕЛЕМЕТРИИ). Анализа с помощью Sysinternals (Handle.exe и ListDLLs.exe)
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Конфликт с общей конфигурации IIS

Если у вас есть кластер из веб-серверов, возможно, вы используете [общей конфигурации](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211). Мы не можем вставить автоматически наших HttpModule в этой общей конфигурации. Каждый веб-сервер сначала необходимо выполнить команду Enable Установка нашей библиотеки DLL в его глобальный кэш СБОРОК.

После выполнения команды Enable 
1. Перейдите в каталог общей конфигурации и найти ваш `applicationHost.config` файл.
2. Добавьте следующую строку в конфигурацию в раздел модулей:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>Устранение неполадок
    
### <a name="troubleshooting-powershell"></a>Устранение неполадок PowerShell

#### <a name="how-to-inspect-what-modules-are-available"></a>Как проверить, какие модули доступны?
Можно проводить аудит установленных модулей с помощью команды: `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>Как импортировать модуль в текущий сеанс?
Если модуль не была загружена в сеансе PowerShell, можно вручную загрузить с помощью команды: `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Устранение неполадок модуля v2 монитор состояния

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>Как проверить, какие команды доступны в модуле v2 монитор состояния?
- Выполните команду: `Get-Command -Module Az.ApplicationMonitor` для получения доступных команд:

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>Какова текущая версия модуля v2 монитор состояния?
- Выполните команду: `Get-ApplicationInsightsMonitoringStatus` для получения выходных данных сведения об этом модуле:
    - Версия модуля PowerShell
    - Версия пакета SDK для Insights приложения
    - Пути к файлам модуля PowerShell
    
Просмотрите наши [Справочник по API](status-monitor-v2-api-get-status.md) подробное описание того, как с помощью этого командлета.



### <a name="troubleshooting-running-processes"></a>Устранение неполадок процессов

Вы можете проверить процесс на компьютере инструментированного ли все библиотеки DLL загружаются.
Если мониторинг работает, по крайней мере 12 библиотеки DLL должны быть загружены.

- Cmd: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

Просмотрите наши [Справочник по API](status-monitor-v2-api-get-status.md) подробное описание того, как с помощью этого командлета.


### <a name="collect-etw-logs-with-perfview"></a>Сбор журналов трассировки событий Windows с помощью PerfView

#### <a name="setup"></a>Настройка

1. Скачайте PerfView.exe и PerfView64.exe из https://github.com/Microsoft/perfview/releases
2. Запустите PerfView64.exe
3. Разверните узел «Дополнительные параметры»
4. Снимите флажок:
    - ZIP
    - Объединить
    - Коллекция символов .NET
5. Установите дополнительные поставщики: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Собираем журналы

1. В консоли команд с правами администратора, выполните `iisreset /stop` отключение IIS и всех веб-приложений.
2. В PerfView нажмите кнопку «Начать сбор»
3. В консоли команд с правами администратора, выполните `iisreset /start` запуск сервера IIS.
4. Попробуйте перейти к своему приложению.
5. После завершения загрузки приложения вернитесь PerfView и нажмите кнопку «Остановить сбор данных»



## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите наши [Справочник по API](status-monitor-v2-overview.md#powershell-api-reference) найти параметр, вы не решены.
- Если вы столкнетесь с проблемой, не перечисленные здесь, можно связаться с нами [здесь](https://github.com/Microsoft/ApplicationInsights-Home/issues).
