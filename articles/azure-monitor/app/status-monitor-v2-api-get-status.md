---
title: Ссылка на API агентства API для анализа приложений приложений Azure
description: Ссылка на API агента приложений. Получить-ApplicationInsightsМониторинг. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 159dab4a228c822ef62c45c9ccceff638a9bea45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671262"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>Приложение Insights Агент API: Get-ApplicationInsightsMonitoringStatus

В этой статье описывается cmdlet, который является членом [модуля Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Это cmdlet предоставляет информацию о устранении неполадок о Status Monitor.
Используйте этот cmdlet для исследования состояния мониторинга, версии модуля PowerShell и для проверки процесса выполнения.
В этом cmdlet будет представлена информация и информация о ключевых файлах, необходимых для мониторинга.

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin.

## <a name="examples"></a>Примеры

### <a name="example-application-status"></a>Пример: Статус приложения

Запустите `Get-ApplicationInsightsMonitoringStatus` команду для отображения состояния мониторинга веб-сайтов.

```
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
- **Идентификатор машины** — это анонимный идентификатор, используемый для однозначной идентификации вашего сервера. При создании запроса на поддержку нам понадобится этот идентификатор для поиска журналов для вашего сервера.
- **Веб-узел по умолчанию** остановлен в IIS
- **DemoWebApp111** был запущен в IIS, но не получил никаких запросов. Этот отчет показывает, что нет запущенного процесса (ProcessId: не найдено).
- **DemoWebApp222** работает и находится под наблюдением (Инструмент: правда). Основываясь на пользовательской конфигурации, инструментарий ключ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx123 был сопоставлен для этого сайта.
- **DemoWebApp333** был вручную инструментирован с помощью приложения Исследования SDK. Status Monitor обнаружил SDK и не будет контролировать этот сайт.


### <a name="example-powershell-module-information"></a>Пример: Информация о модуле PowerShell

Запустите `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` команду для отображения информации о текущем модуле:

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

### <a name="example-runtime-status"></a>Пример: Состояние продолжительности выполнения

Вы можете проверить процесс на приборном компьютере, чтобы увидеть, если все DLLs загружаются. Если мониторинг работает, необходимо загрузить не менее 12 DLL.

Выполнить `Get-ApplicationInsightsMonitoringStatus -InspectProcess`команду:


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

По умолчанию этот cmdlet сообщит о состоянии мониторинга веб-приложений.
Используйте эту опцию для проверки, если ваша заявка была успешно инструментальной.
Вы также можете просмотреть, какой ключ инструментария был сопоставлен с вашим сайтом.


### <a name="-powershellmodule"></a>-PowerShellМодуль
**Необязательно**. Используйте этот переключатель, чтобы сообщить номера версий и пути DLL, необходимые для мониторинга.
Используйте эту опцию, если вам нужно определить версию любого DLL, включая Application Insights SDK.

### <a name="-inspectprocess"></a>-InspectProcess

**Необязательно**. Используйте этот переключатель, чтобы сообщить, работает ли IIS.
Он также будет загружать внешние инструменты, чтобы определить, если необходимые DLLs загружаются в время выполнения IIS.


Если этот процесс не удается по какой-либо причине, вы можете запустить эти команды вручную:
- iisreset.exe /статус
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp findstr /I "ИнструментированиеEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp findstr /I "ИнструментированиеEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Необязательно**. Используется только с InspectProcess. Используйте этот переключатель, чтобы пропустить запрос пользователя, который появляется перед загрузкой дополнительных инструментов.


## <a name="next-steps"></a>Дальнейшие действия

 Сделайте больше с агентом По анализу приложений:
 - Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
