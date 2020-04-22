---
title: Ссылка на API aPI aPI aPI aPI агентства приложений Azure
description: Ссылка на API агента приложений. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733678"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Ссылка на API-агента API для мониторинга приложений Azure Monitor

В этой статье описывается cmdlet, который является членом [модуля Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Для начала, вам нужен ключ приборов. Для получения дополнительной информации [см.](create-new-resource.md#copy-the-instrumentation-key)
> - Это cmdlet требует, чтобы вы рассмотрели и приняли наши лицензии и конфиденциальности заявление.

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin и повышенной политикой исполнения. Для получения дополнительной информации [см.](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)
> - Это cmdlet требует, чтобы вы рассмотрели и приняли наши лицензии и конфиденциальности заявление.
> - Двигатель приборов добавляет дополнительные накладные расходы и выключен по умолчанию.


## <a name="enable-instrumentationengine"></a>Включить-ИнструментированиеEngine

Включает приборовоз, установив некоторые ключи реестра.
Перезапустите IIS для вхотворемых изменений.

Аппаратный механизм может дополнять данные, собранные SDK .NET.
Он собирает события и сообщения, описывающие выполнение управляемого процесса. Эти события и сообщения включают коды результатов зависимости, глаголы HTTP и [текст команды S'L.](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)

Включите приборный двигатель, если:
- Вы уже включили мониторинг с помощью Смдлета, но не включили приборный двигатель.
- Вы вручную сотворили свое приложение с помощью SDK .NET и хотите получить дополнительную телеметрию.

### <a name="examples"></a>Примеры

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Параметры

#### <a name="-acceptlicense"></a>-ПринятьЛицензия
**Необязательный параметр.** Используйте этот переключатель, чтобы принять заявление о лицензии и конфиденциальности в безголовых установках.

#### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для вывода подробные журналы.

### <a name="output"></a>Выходные данные


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Пример выхода из успешного включения приборного двигателя

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Включить-ApplicationInsightsМониторинг

Позволяет бескодно прикрепить мониторинг приложений IIS на целевом компьютере.

Это cmdlet изменит iIS applicationHost.config и установит некоторые ключи реестра.
Он также создаст файл applicationinsights.ikey.config, который определяет ключ приборов, используемый каждым приложением.
IIS загрузит RedfieldModule на запуск, который будет вводить Application Insights SDK в приложения по мере запуска приложений.
Перезапустите IIS для вхастывания изменений.

После включения мониторинга мы рекомендуем использовать [Live Metrics,](live-stream.md) чтобы быстро проверить, отправляет ли ваше приложение нам телеметрию.

### <a name="examples"></a>Примеры

#### <a name="example-with-a-single-instrumentation-key"></a>Пример с одним ключом приборов
В этом примере всем приложениям на текущем компьютере назначается единый ключ прибора.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Пример с картой ключей приборов
В данном примере:
- `MachineFilter`соответствует текущему компьютеру `'.*'` с помощью подстановочного знака.
- `AppFilter='WebAppExclude'`обеспечивает `null` ключ приборов. Указанное приложение не будет инструментальным.
- `AppFilter='WebAppOne'`присваивает указанному приложению уникальный ключ приборов.
- `AppFilter='WebAppTwo'`присваивает указанному приложению уникальный ключ приборов.
- Наконец, `AppFilter` также `'.*'` использует подстановочный знак, чтобы соответствовать всем веб-приложениям, которые не соответствуют предыдущим правилам, и назначить ключ приборов по умолчанию.
- Пространства добавляются для читаемости.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>Параметры

#### <a name="-instrumentationkey"></a>-ИнструментацияКей
**Обязательно.** Используйте этот параметр для предоставления единого ключа приборов для использования всеми приложениями на целевом компьютере.

#### <a name="-instrumentationkeymap"></a>-ИнструментацияКейМап
**Обязательно.** Используйте этот параметр для предоставления нескольких ключей приборов и отображения ключей приборов, используемых каждым приложением.
Вы можете создать единый сценарий `MachineFilter`установки для нескольких компьютеров, установив.

> [!IMPORTANT]
> Приложения будут соответствовать правилам в порядке предоставления правил. Таким образом, вы должны указать наиболее конкретные правила первого и наиболее общие правила последнего.

##### <a name="schema"></a>схема
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** является обязательным регистратором компьютера или VM-именем.
    - '.' будет соответствовать всем
    - 'ComputerName' будет соответствовать только компьютеры с точное имя указано.
- **AppFilter** является обязательным регистратором сайта IIS. Вы можете получить список сайтов на вашем сервере, запустив команду [get-iissite.](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)
    - '.' будет соответствовать всем
    - 'SiteName' будет соответствовать только сайту IIS с указанным точное имя.
- **InstrumentationKey** необходим для мониторинга приложений, которые соответствуют предыдущим двум фильтрам.
    - Оставьте это значение недействительным, если вы хотите определить правила, чтобы исключить мониторинг.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Необязательный параметр.** Используйте этот переключатель, чтобы позволить аппаратному механизму собирать события и сообщения о том, что происходит во время выполнения управляемого процесса. Эти события и сообщения включают коды результатов зависимостей, глаголы HTTP и командный текст S'L.

Двигатель приборов добавляет накладные расходы и выключен по умолчанию.

#### <a name="-acceptlicense"></a>-ПринятьЛицензия
**Необязательный параметр.** Используйте этот переключатель, чтобы принять заявление о лицензии и конфиденциальности в безголовых установках.

#### <a name="-ignoresharedconfig"></a>-ИгнорДжесКЕклиг
При использовании кластера веб-серверов может быть [использоваться общая конфигурация.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
HttpModule не может быть введен в эту общую конфигурацию.
Этот скрипт выйдет из строя с сообщением о том, что требуются дополнительные шаги установки.
Используйте этот переключатель, чтобы игнорировать эту проверку и продолжить установку предпосылок. Для получения дополнительной информации [см.](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для отображения подробных журналов.

#### <a name="-whatif"></a>-WhatIf 
**Общий параметр.** Используйте этот переключатель для тестирования и проверки параметров ввода без фактического включения мониторинга.

### <a name="output"></a>Выходные данные

#### <a name="example-output-from-a-successful-enablement"></a>Пример выхода из успешного включения

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Отмашина-ИнструментацияEngine

Отключает приборный механизм, удаляя некоторые ключи реестра.
Перезапустите IIS для вхотворемых изменений.

### <a name="examples"></a>Примеры

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Параметры 

#### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для вывода подробные журналы.

### <a name="output"></a>Выходные данные


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Пример выхода от успешного отключения приборного двигателя

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Отключить-ApplicationInsightsМониторинг

Отработка мониторинга на целевом компьютере.
Это cmdlet удалит изменение в IIS applicationHost.config и удалит ключи реестра.

### <a name="examples"></a>Примеры

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Параметры 

#### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для отображения подробных журналов.

### <a name="output"></a>Выходные данные


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Пример выхода из успешного отключения мониторинга

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Получает файл конфигурации и печатает значения на консоль.

### <a name="examples"></a>Примеры

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Параметры

Параметры не требуются.

### <a name="output"></a>Выходные данные


##### <a name="example-output-from-reading-the-config-file"></a>Пример вывода из чтения файла конфигурации

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsМониторинг

Это cmdlet предоставляет информацию о устранении неполадок о Status Monitor.
Используйте этот cmdlet для исследования состояния мониторинга, версии модуля PowerShell и для проверки процесса выполнения.
В этом cmdlet будет представлена информация и информация о ключевых файлах, необходимых для мониторинга.

### <a name="examples"></a>Примеры

#### <a name="example-application-status"></a>Пример: Статус приложения

Запустите `Get-ApplicationInsightsMonitoringStatus` команду для отображения состояния мониторинга веб-сайтов.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

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


#### <a name="example-powershell-module-information"></a>Пример: Информация о модуле PowerShell

Запустите `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` команду для отображения информации о текущем модуле:

```powershell

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

#### <a name="example-runtime-status"></a>Пример: Состояние продолжительности выполнения

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

### <a name="parameters"></a>Параметры

#### <a name="no-parameters"></a>(Без параметров)

По умолчанию этот cmdlet сообщит о состоянии мониторинга веб-приложений.
Используйте эту опцию для проверки, если ваша заявка была успешно инструментальной.
Вы также можете просмотреть, какой ключ инструментария был сопоставлен с вашим сайтом.


#### <a name="-powershellmodule"></a>-PowerShellМодуль
**Необязательно**. Используйте этот переключатель, чтобы сообщить номера версий и пути DLL, необходимые для мониторинга.
Используйте эту опцию, если вам нужно определить версию любого DLL, включая Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspectProcess

**Необязательно**. Используйте этот переключатель, чтобы сообщить, работает ли IIS.
Он также будет загружать внешние инструменты, чтобы определить, если необходимые DLLs загружаются в время выполнения IIS.


Если этот процесс не удается по какой-либо причине, вы можете запустить эти команды вручную:
- iisreset.exe /статус
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp findstr /I "ИнструментированиеEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp findstr /I "ИнструментированиеEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Необязательно**. Используется только с InspectProcess. Используйте этот переключатель, чтобы пропустить запрос пользователя, который появляется перед загрузкой дополнительных инструментов.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Устанавливает файл конфигурации, не делая полной переустановки.
Перезапустите IIS для вхастывания изменений.

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin.


### <a name="examples"></a>Примеры

#### <a name="example-with-a-single-instrumentation-key"></a>Пример с одним ключом приборов
В этом примере всем приложениям на текущем компьютере будет назначен единый ключ прибора.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Пример с картой ключей приборов
В данном примере:
- `MachineFilter`соответствует текущему компьютеру `'.*'` с помощью подстановочного знака.
- `AppFilter='WebAppExclude'`обеспечивает `null` ключ приборов. Указанное приложение не будет инструментальным.
- `AppFilter='WebAppOne'`присваивает указанному приложению уникальный ключ приборов.
- `AppFilter='WebAppTwo'`присваивает указанному приложению уникальный ключ приборов.
- Наконец, `AppFilter` также `'.*'` использует подстановочный знак, чтобы соответствовать всем веб-приложениям, которые не соответствуют предыдущим правилам, и назначить ключ приборов по умолчанию.
- Пространства добавляются для читаемости.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Параметры

#### <a name="-instrumentationkey"></a>-ИнструментацияКей
**Обязательно.** Используйте этот параметр для предоставления единого ключа приборов для использования всеми приложениями на целевом компьютере.

#### <a name="-instrumentationkeymap"></a>-ИнструментацияКейМап
**Обязательно.** Используйте этот параметр для предоставления нескольких ключей приборов и отображения ключей приборов, используемых каждым приложением.
Вы можете создать единый сценарий `MachineFilter`установки для нескольких компьютеров, установив.

> [!IMPORTANT]
> Приложения будут соответствовать правилам в порядке предоставления правил. Таким образом, вы должны указать наиболее конкретные правила первого и наиболее общие правила последнего.

##### <a name="schema"></a>схема
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** является обязательным регистратором компьютера или VM-именем.
    - '.' будет соответствовать всем
    - 'ComputerName' будет сопоставлять только компьютеры с указанным именем.
- **AppFilter** является обязательным регистратором компьютера или VM-именем.
    - '.' будет соответствовать всем
    - 'ApplicationName' будет соответствовать только приложениям IIS с указанным именем.
- **InstrumentationKey** требуется для обеспечения мониторинга приложений, которые соответствуют предыдущим двум фильтрам.
    - Оставьте это значение недействительным, если вы хотите определить правила, чтобы исключить мониторинг.


#### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для отображения подробных журналов.


### <a name="output"></a>Выходные данные

По умолчанию, нет вывода.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Пример многословного вывода от установки конфигурационного файла через -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Пример многословного вывода от установки конфигурационного файла через -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Старт-ApplicationInsightsMonitoringTrace

Собирает [события ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) из безкодатого времени выполнения. Этот cmdlet является альтернативой запуску [PerfView](https://github.com/microsoft/perfview).

Собранные события будут распечатаны на консоли в режиме реального времени и сохранены в файле ETL. Выводный файл ETL может быть открыт [PerfView](https://github.com/microsoft/perfview) для дальнейшего изучения.

Этот cmdlet будет работать до тех пор, пока не достигнет длительности тайм-аута (по умолчанию 5 минут) или остановлен вручную ().`Ctrl + C`

### <a name="examples"></a>Примеры

#### <a name="how-to-collect-events"></a>Как собирать события

Обычно мы просим вас собирать события для расследования того, почему ваше приложение не является инструментальным.

Безкодное время выполнения прикрепите будет излучать события ETW при запуске IIS и при запуске приложения.

Для сбора этих событий:
1. В cmd консоли с привилегиями админа `iisreset /stop` выполните, чтобы отключить IIS и все веб-приложения.
2. Выполнить этот cmdlet
3. В cmd консоли с привилегиями админа `iisreset /start` выполните, чтобы начать IIS.
4. Попробуйте просмотреть в приложении.
5. После завершения загрузки приложения можно вручную`Ctrl + C`остановить его () или дождаться тайм-аута.

#### <a name="what-events-to-collect"></a>Какие события собирать

При сборе событий у вас есть три варианта:
1. Используйте `-CollectSdkEvents` переключатель для сбора событий, испускаемых sDK Application Insights.
2. Используйте `-CollectRedfieldEvents` переключатель для сбора событий, испускаемых Status Monitor и Redfield Runtime. Эти журналы полезны при диагностике IIS и запуска приложений.
3. Используйте оба коммутатора для сбора обоих типов событий.
4. По умолчанию, если коммутатор не указан, оба типа событий будут собраны.


### <a name="parameters"></a>Параметры

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Необязательный параметр.** Используйте этот параметр, чтобы установить, как долго этот скрипт должен собирать события. Значение по умолчанию — 5 минут.

#### <a name="-logdirectory"></a>-ЛогДиректори
**Необязательный параметр.** Используйте этот переключатель для настройки каталога вывода файла ETL. По умолчанию этот файл будет создан в каталоге модулей PowerShell. Полный путь будет отображаться во время выполнения сценария.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Необязательный параметр.** Используйте этот переключатель для сбора событий Application Insights SDK.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Необязательный параметр.** Используйте этот переключатель для сбора событий из Status Monitor и времени выполнения Redfield.

#### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для вывода подробные журналы.



### <a name="output"></a>Выходные данные


#### <a name="example-of-application-startup-logs"></a>Пример журналов запуска приложений
```powershell
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

## <a name="next-steps"></a>Следующие шаги

  Просмотр телеметрии:
 - [Изучите метрики](../../azure-monitor/app/metrics-explorer.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- Используйте [аналитику](../../azure-monitor/app/analytics.md) для более продвинутых запросов.
- [Создание панелей мониторинга.](../../azure-monitor/app/overview-dashboard.md)
 
 Добавление данных телеметрии:
 - [Создайте веб-тесты](monitor-web-app-availability.md), чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента,](../../azure-monitor/app/javascript.md) чтобы увидеть исключения из кода веб-страницы и включить отслеживание вызовов.
- [Добавьте SDK Application Insights в свой код,](../../azure-monitor/app/asp-net.md) чтобы вы могли вставлять вызовы трассировки и регистрации.
 
 Сделайте больше с агентом По анализу приложений:
 - Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.






