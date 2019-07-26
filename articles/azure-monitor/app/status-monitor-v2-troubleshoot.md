---
title: Устранение неполадок в Azure монитор состояния v2 и известные проблемы | Документация Майкрософт
description: Известные проблемы монитор состояния v2 и примеры устранения неполадок. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
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
ms.openlocfilehash: e34beba32eace370664893225dd85b6f4b79c886
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424101"
---
# <a name="troubleshooting-status-monitor-v2"></a>Устранение неполадок монитор состояния v2

При включении мониторинга могут возникнуть проблемы, препятствующие сбору данных.
В этой статье перечислены все известные проблемы и приведены примеры устранения неполадок.
Если вы перейдете по вопросу, не указанному здесь, вы можете связаться с нами на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Известные проблемы

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Конфликтующие библиотеки DLL в каталоге Bin приложения

Если какая-либо из этих библиотек DLL находится в каталоге bin, мониторинг может завершиться ошибкой:

- Microsoft. ApplicationInsights. dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System. Diagnostics. DiagnosticSource. dll

Некоторые из этих библиотек DLL включены в шаблоны приложений Visual Studio по умолчанию, даже если приложение не использует их.
Для просмотра признаком поведения можно использовать средства устранения неполадок.

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- Передача данных из IISReset и приложений (без телеметрии). Исследование с помощью Sysinternals (Handle. exe и ListDLLs. exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Конфликт с общей конфигурацией IIS

Если у вас есть кластер веб-серверов, возможно, используется [Общая конфигурация](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Не удается внедрить HttpModule в эту общую конфигурацию.
Выполните команду enable на каждом веб-сервере, чтобы установить библиотеку DLL в глобальный кэш сборок каждого сервера.

После выполнения команды Enable выполните следующие действия.
1. Перейдите к общему каталогу конфигурации и найдите файл applicationHost. config.
2. Добавьте эту строку в раздел modules вашей конфигурации:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Вложенные приложения IIS

Мы не инструментирован вложенные приложения в IIS версии 1,0.
Мы отслеживаем эту ошибку [здесь](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>Расширенная конфигурация пакета SDK недоступна.

Конфигурация пакета SDK не предоставляется конечному пользователю в версии 1,0.
Мы отслеживаем эту ошибку [здесь](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>Устранение неполадок
    
### <a name="troubleshooting-powershell"></a>Устранение неполадок PowerShell

#### <a name="determine-which-modules-are-available"></a>Определение доступных модулей
Чтобы определить, какие `Get-Module -ListAvailable` модули установлены, можно использовать команду.

#### <a name="import-a-module-into-the-current-session"></a>Импорт модуля в текущий сеанс
Если модуль не был загружен в сеанс PowerShell, его можно загрузить вручную с помощью `Import-Module <path to psd1>` команды.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Устранение неполадок модуля монитор состояния v2

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Список команд, доступных в модуле монитор состояния v2
Выполните команду `Get-Command -Module Az.ApplicationMonitor` , чтобы получить доступные команды:

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

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Определение текущей версии модуля монитор состояния v2
`Get-ApplicationInsightsMonitoringStatus -PowerShellModule` Выполните команду, чтобы отобразить следующие сведения о модуле:
   - Версия модуля PowerShell
   - Версия пакета SDK Application Insights
   - Пути к файлам модуля PowerShell
    
Подробное описание использования этого командлета см. в справочнике по [API](status-monitor-v2-api-get-status.md) .


### <a name="troubleshooting-running-processes"></a>Устранение неполадок выполняющихся процессов

Можно проверить процессы на инструментированном компьютере, чтобы определить, загружены ли все библиотеки DLL.
Если наблюдение работает, необходимо загрузить не менее 12 библиотек DLL.

`Get-ApplicationInsightsMonitoringStatus -InspectProcess` Используйте команду для проверки библиотек DLL.

Подробное описание использования этого командлета см. в справочнике по [API](status-monitor-v2-api-get-status.md) .


### <a name="collect-etw-logs-by-using-perfview"></a>Получение журналов ETW с помощью PerfView

#### <a name="setup"></a>Установка

1. Скачайте PerfView. exe и PerfView64. exe с сайта [GitHub](https://github.com/Microsoft/perfview/releases).
2. Запустите PerfView64. exe.
3. Разверните узел **Дополнительные параметры**.
4. Снимите эти флажки:
    - **Архиваци**
    - **AutoMerge**
    - **Коллекция символов .NET**
5. Задайте следующие **дополнительные поставщики**:`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Собираем журналы

1. В командной консоли с правами администратора выполните `iisreset /stop` команду, чтобы отключить службы IIS и все веб-приложения.
2. В PerfView выберите **начать сбор**.
3. В командной консоли с правами администратора выполните `iisreset /start` команду, чтобы запустить службы IIS.
4. Попробуйте перейти к приложению.
5. После загрузки приложения вернитесь в PerfView и выберите команду " **Закрыть коллекцию**".



## <a name="next-steps"></a>Следующие шаги

- Ознакомьтесь со [справочником по API](status-monitor-v2-overview.md#powershell-api-reference) , чтобы узнать о параметрах, которые могли быть пропущены.
- Если вы перейдете по вопросу, не указанному здесь, вы можете связаться с нами на [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
