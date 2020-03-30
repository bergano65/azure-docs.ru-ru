---
title: Azure Application Insights Agent устранение неполадок и известные проблемы Документы Майкрософт
description: Известные проблемы агента Application Insights и примеры устранения неполадок. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 217629ba5c386557455cc2d2b8bd47f85fa8f84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671160"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Устранение неполадок Приложение Исследования агент (ранее назывался Статус монитор v2)

При включании мониторинга могут возникнуть проблемы, препятствующие сбору данных.
В этой статье перечислены все известные проблемы и приведены примеры устранения неполадок.
Если вы столкнетесь с проблемой, которая не указана здесь, вы можете связаться с нами на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Известные проблемы

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Конфликт у DLLs в каталоге бен приложения

Если какой-либо из этих DLLs присутствует в каталоге бен, мониторинг может потерпеть неудачу:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Некоторые из этих DLLs включены в шаблоны приложений Visual Studio по умолчанию, даже если приложение их не использует.
Вы можете использовать средства устранения неполадок, чтобы увидеть симптоматическое поведение:

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

- IISReset и загрузка приложения (без телеметрии). Исследуйте с Sysinternals (Handle.exe и ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Конфликт с общей конфигурацией IIS

Если у вас есть кластер веб-серверов, возможно, вы используете [общую конфигурацию.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
HttpModule не может быть введен в эту общую конфигурацию.
Запустите команду Включить на каждом веб-сервере, чтобы установить DLL в GAC каждого сервера.

После запуска команды Enable выполните следующие действия:
1. Перейдите в общий каталог конфигурации и найдите файл applicationHost.config.
2. Добавьте эту строку в раздел модулей конфигурации:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS Nested Приложения

Мы не инструментировали вложенные приложения в IIS в версии 1.0.
Мы отслеживаем эту проблему [здесь](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>Расширенная конфигурация SDK недоступна.

Конфигурация SDK не подвергается воздействию конечного пользователя в версии 1.0.
Мы отслеживаем эту проблему [здесь](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>Устранение неполадок
    
### <a name="troubleshooting-powershell"></a>Устранение неполадок PowerShell

#### <a name="determine-which-modules-are-available"></a>Определить, какие модули доступны
Вы можете `Get-Module -ListAvailable` использовать команду, чтобы определить, какие модули установлены.

#### <a name="import-a-module-into-the-current-session"></a>Импорт модуля в текущую сессию
Если модуль не был загружен в сеанс PowerShell, его можно загрузить `Import-Module <path to psd1>` вручную с помощью команды.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Устранение проблем с модулем «Агент анализа приложений»

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Перечислите команды, доступные в модуле Агента Анализа Приложений
Запустите `Get-Command -Module Az.ApplicationMonitor` команду, чтобы получить доступные команды:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Определите текущую версию модуля Агента Анализа Приложений
Запустите `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` команду для отображения следующей информации о модуле:
   - Версия модуля PowerShell
   - Версия Приложения Исследования SDK
   - Файловые пути модуля PowerShell
    
Просмотрите [ссылку API,](status-monitor-v2-api-get-status.md) чтобы подробно опоставить описание того, как использовать этот cmdlet.


### <a name="troubleshooting-running-processes"></a>Устранение проблемных процессов

Вы можете проверить процессы на приборном компьютере, чтобы определить, все ли DLL загружены.
Если мониторинг работает, необходимо загрузить не менее 12 DLL.

Используйте `Get-ApplicationInsightsMonitoringStatus -InspectProcess` команду, чтобы проверить DLLs.

Просмотрите [ссылку API,](status-monitor-v2-api-get-status.md) чтобы подробно опоставить описание того, как использовать этот cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Сбор журналов ETW с помощью PerfView

#### <a name="setup"></a>Настройка

1. Скачать PerfView.exe и PerfView64.exe от [GitHub](https://github.com/Microsoft/perfview/releases).
2. Начните PerfView64.exe.
3. Разверните **Дополнительные параметры**.
4. Очистить эти флажки:
    - **Молния**
    - **Объединить**
    - **Коллекция символов .NET**
5. Установите **дополнительные поставщики:**`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Сбор журналов

1. В командной консоли с привилегиями Admin запустите `iisreset /stop` команду, чтобы отключить IIS и все веб-приложения.
2. В PerfView выберите **Start Collection**.
3. В командной консоли с привилегиями Admin запустите `iisreset /start` команду для запуска IIS.
4. Попробуйте просмотреть в приложении.
5. После загрузки приложения вернитесь в PerfView и выберите **Stop Collection.**



## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите [ссылку API,](status-monitor-v2-overview.md#powershell-api-reference) чтобы узнать о параметрах, которые вы могли пропустить.
- Если вы столкнетесь с проблемой, которая не указана здесь, вы можете связаться с нами на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
