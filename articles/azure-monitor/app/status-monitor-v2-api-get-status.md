---
title: 'Монитор состояния v2 Справочник по API Azure: Получение состояния | Документация Майкрософт'
description: Справочник по API v2 монитор состояния. Get-ApplicationInsightsMonitoringStatus. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: e579db587d5f56aecd60f584ea4805dd4ac1bf98
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718362"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v040-alpha"></a>Состояние монитора v2 API: Get-ApplicationInsightsMonitoringStatus (v0.4.0-альфа-версия)

В этой статье описывается командлет, который является членом [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Описание

Этот командлет предоставляет сведения об устранении неполадок, о мониторе состояния.
Этот командлет можно используйте для изучения состояние мониторинга версию модуля PowerShell и для проверки выполняющегося процесса.
Этот командлет сообщает сведения о версии и сведения о файлах ключей, необходимые для отслеживания.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

## <a name="examples"></a>Примеры

### <a name="example-application-status"></a>Пример: Состояние приложения

Выполните команду `Get-ApplicationInsightsMonitoringStatus` для отображения состояние мониторинга веб-сайтов.

```
Machine Identifier:
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

В этом примере;
- **Идентификатор компьютера** является анонимный идентификатор, используемый для идентификации сервера. Если вы создаете запрос в службу поддержки, нам потребуется этот идентификатор, чтобы найти журналы для вашего сервера.
- **По умолчанию веб-сайта** остановлен в IIS
- **DemoWebApp111** была запущена в IIS, но еще не получил все запросы. Этот отчет показывает, что не выполняющегося процесса (ProcessId: не найден).
- **DemoWebApp222** работает и отслеживается (Instrumented: true). Исходя из конфигурации пользователя, ключ инструментирования xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 обнаружено соответствие для этого сайта.
- **DemoWebApp333** вручную инструментирования с помощью пакета SDK Application Insights. Монитор состояния обнаружен пакет SDK и не будет проверять этот сайт.


### <a name="example-powershell-module-information"></a>Пример: Сведения о модуле PowerShell

Выполните команду `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` для отображения сведений о текущем модуле:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

### <a name="example-runtime-status"></a>Пример: Состояние среды выполнения

Вы можете проверить процесс на компьютере инструментированного ли все библиотеки DLL загружаются. Если мониторинг работает, по крайней мере 12 библиотеки DLL должны быть загружены.

Выполните команду `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>Параметры

### <a name="no-parameters"></a>(Без параметров)

По умолчанию этот командлет сообщает состояние мониторинга веб-приложений.
Используйте этот параметр, чтобы просмотреть, если приложение успешно инструментировано.
Вы также можете просмотреть которого обнаружено соответствие ключ инструментирования для вашего сайта.


### <a name="-powershellmodule"></a>-PowerShellModule
**Необязательный**. Используйте этот параметр, чтобы сообщить номера версий и путей к DLL-библиотеки, необходимые для отслеживания.
Используйте этот параметр, если вам нужно определить версию любой библиотеки DLL, в том числе пакет SDK Application Insights.

### <a name="-inspectprocess"></a>-InspectProcess

**Необязательный**. Этот параметр можно используйте для отчетов, работает ли службы IIS.
Также будут загружены внешние средства, чтобы определить необходимые библиотеки DLL загружаются в среду выполнения служб IIS.


Если этот процесс завершается ошибкой по любой причине, можно вручную выполните следующие команды:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) w3wp -p | findstr /I «InstrumentationEngine искусственного Интеллекта. ApplicationInsights»
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Необязательный**. Используется только с InspectProcess. Используйте этот параметр, чтобы пропустить запрос к пользователю, которое появляется перед загружаются дополнительные средства.


## <a name="next-steps"></a>Следующие шаги

 Новые возможности в версии 2 монитор состояния:
 - Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
