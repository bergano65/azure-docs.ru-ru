---
title: Монитор состояния v2 Устранение неполадок Azure и известных проблем | Документация Майкрософт
description: Известные проблемы в версии 2 монитор состояния и устранения неполадок примеры. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложений ASP.NET, размещенным на предприятиях, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: df59766ce38ac81568570cd6544ee28808ff8249
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807014"
---
# <a name="troubleshooting-status-monitor-v2"></a>Устранение неполадок с состоянием отслеживать версии 2

При включении мониторинга могут возникнуть проблемы, препятствующие сбора данных.
В этой статье перечислены все известные проблемы и приводятся примеры устранения неполадок.
Если вам встретится проблема, которая отсутствует в списке, вы можете обратиться к нам на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Известные проблемы

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Конфликтующие библиотек DLL в каталог bin приложения

Если любой из этих библиотек DLL в каталоге bin, мониторинга может завершиться ошибкой.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Некоторые из этих библиотек DLL включены в шаблоны приложений по умолчанию Visual Studio, даже если приложение не использует их.
Средства устранения неполадок можно использовать для просмотра признаков поведение:

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

- IISReset и приложение загрузить (без телеметрии). Анализ с помощью Sysinternals (Handle.exe и ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Конфликт с общей конфигурации IIS

Если у вас есть кластер из веб-серверов, возможно, вы используете [общей конфигурации](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Модуль HttpModule не могут быть добавлены в этой общей конфигурации.
Выполните команду Enable на каждом веб-сервере, чтобы установить библиотеку DLL в глобальный кэш СБОРОК каждого сервера.

После выполнения команды Enable, выполните следующие действия:
1. Перейдите в каталог общей конфигурации и найдите файл applicationHost.config.
2. Добавьте следующую строку в раздел модули конфигурации:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Устранение неполадок
    
### <a name="troubleshooting-powershell"></a>Устранение неполадок PowerShell

#### <a name="determine-which-modules-are-available"></a>Определить, какие модули доступны
Можно использовать `Get-Module -ListAvailable` команду, чтобы определить, какие модули устанавливаются.

#### <a name="import-a-module-into-the-current-session"></a>Импорт модуля в текущий сеанс
Если модуль не была загружена в сеансе PowerShell, можно вручную загрузить его с помощью `Import-Module <path to psd1>` команды.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Устранение неполадок модуля v2 монитор состояния

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Список команд, доступных в модуле v2 монитор состояния
Выполните команду `Get-Command -Module Az.ApplicationMonitor` для получения доступных команд:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Определить текущую версию модуля v2 монитор состояния
Запустите `Get-ApplicationInsightsMonitoringStatus` команду, чтобы отобразить следующие сведения о модуле:
   - Версия модуля PowerShell
   - Версия пакета SDK для Insights приложения
   - Пути к файлам модуля PowerShell
    
Просмотрите [Справочник по API](status-monitor-v2-api-get-status.md) подробное описание того, как с помощью этого командлета.


### <a name="troubleshooting-running-processes"></a>Устранение неполадок процессов

Вы можете проверить процессы на инструментированного компьютер, чтобы определить, если все библиотеки DLL загружаются.
Если мониторинг работает, по крайней мере 12 библиотеки DLL должны быть загружены.

Используйте `Get-ApplicationInsightsMonitoringStatus -InspectProcess` команду, чтобы проверить библиотеки DLL.

Просмотрите [Справочник по API](status-monitor-v2-api-get-status.md) подробное описание того, как с помощью этого командлета.


### <a name="collect-etw-logs-by-using-perfview"></a>Сбор журналов трассировки событий Windows с помощью PerfView

#### <a name="setup"></a>Установка

1. Скачайте PerfView.exe и PerfView64.exe из [GitHub](https://github.com/Microsoft/perfview/releases).
2. Запустите PerfView64.exe.
3. Разверните **Дополнительные параметры**.
4. Снимите следующие флажки:
    - **ZIP**
    - **Слияние**
    - **Коллекция символов .NET**
5. Задать **дополнительные поставщики**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Сбор журналов

1. В консоль командной строки с правами администратора, запустите `iisreset /stop` команду, чтобы отключить IIS и всех веб-приложений.
2. В PerfView, выберите **начать сбор**.
3. В консоль командной строки с правами администратора, запустите `iisreset /start` команду, чтобы запустить службы IIS.
4. Попробуйте перейти к своему приложению.
5. После загрузки приложения, вернуться в PerfView и выберите **остановить сбор данных**.



## <a name="next-steps"></a>Следующие шаги

- Просмотрите [Справочник по API](status-monitor-v2-overview.md#powershell-api-reference) Дополнительные сведения о параметрах, вы не решены.
- Если вам встретится проблема, которая отсутствует в списке, вы можете обратиться к нам на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
