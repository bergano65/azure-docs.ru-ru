---
title: Ссылка на API агентства API для анализа приложений приложений Azure
description: Ссылка на API агента приложений. Включить-ApplicationInsightsМониторинг. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: f5d66c5c21f7491e3bdc6bb70f693c3b98bf62dd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536749"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Приложение Insights Агент API: Включить-ApplicationInsightsMonitoring

В этой статье описывается cmdlet, который является членом [модуля Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Позволяет бескодно прикрепить мониторинг приложений IIS на целевом компьютере.

Это cmdlet изменит iIS applicationHost.config и установит некоторые ключи реестра.
Он также создаст файл applicationinsights.ikey.config, который определяет ключ приборов, используемый каждым приложением.
IIS загрузит RedfieldModule на запуск, который будет вводить Application Insights SDK в приложения по мере запуска приложений.
Перезапустите IIS для вхастывания изменений.

После включения мониторинга мы рекомендуем использовать [Live Metrics,](live-stream.md) чтобы быстро проверить, отправляет ли ваше приложение нам телеметрию.


> [!NOTE] 
> - Для начала, вам нужен ключ приборов. Для получения дополнительной информации [см.](create-new-resource.md#copy-the-instrumentation-key)
> - Это cmdlet требует, чтобы вы рассмотрели и приняли наши лицензии и конфиденциальности заявление.

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin и повышенной политикой исполнения. Для получения дополнительной информации [см.](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)

## <a name="examples"></a>Примеры

### <a name="example-with-a-single-instrumentation-key"></a>Пример с одним ключом приборов
В этом примере всем приложениям на текущем компьютере назначается единый ключ прибора.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Пример с картой ключей приборов
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


## <a name="parameters"></a>Параметры

### <a name="-instrumentationkey"></a>-ИнструментацияКей
**Обязательно.** Используйте этот параметр для предоставления единого ключа приборов для использования всеми приложениями на целевом компьютере.

### <a name="-instrumentationkeymap"></a>-ИнструментацияКейМап
**Обязательно.** Используйте этот параметр для предоставления нескольких ключей приборов и отображения ключей приборов, используемых каждым приложением.
Вы можете создать единый сценарий `MachineFilter`установки для нескольких компьютеров, установив.

> [!IMPORTANT]
> Приложения будут соответствовать правилам в порядке предоставления правил. Таким образом, вы должны указать наиболее конкретные правила первого и наиболее общие правила последнего.

#### <a name="schema"></a>схема
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** является обязательным регистратором компьютера или VM-именем.
    - '.' будет соответствовать всем
    - 'ComputerName' будет соответствовать только компьютеры с точное имя указано.
- **AppFilter** является обязательным регистратором сайта IIS. Вы можете получить список сайтов на вашем сервере, запустив команду [get-iissite.](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)
    - '.' будет соответствовать всем
    - 'SiteName' будет соответствовать только сайту IIS с указанным точное имя.
- **InstrumentationKey** необходим для мониторинга приложений, которые соответствуют предыдущим двум фильтрам.
    - Оставьте это значение недействительным, если вы хотите определить правила, чтобы исключить мониторинг.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Дополнительные.** Используйте этот переключатель, чтобы позволить аппаратному механизму собирать события и сообщения о том, что происходит во время выполнения управляемого процесса. Эти события и сообщения включают коды результатов зависимостей, глаголы HTTP и командный текст S'L.

Двигатель приборов добавляет накладные расходы и выключен по умолчанию.

### <a name="-acceptlicense"></a>-ПринятьЛицензия
**Дополнительные.** Используйте этот переключатель, чтобы принять заявление о лицензии и конфиденциальности в безголовых установках.

### <a name="-ignoresharedconfig"></a>-ИгнорДжесКЕклиг
При использовании кластера веб-серверов может быть [использоваться общая конфигурация.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
HttpModule не может быть введен в эту общую конфигурацию.
Этот скрипт выйдет из строя с сообщением о том, что требуются дополнительные шаги установки.
Используйте этот переключатель, чтобы игнорировать эту проверку и продолжить установку предпосылок. Для получения дополнительной информации [см.](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для отображения подробных журналов.

### <a name="-whatif"></a>-WhatIf 
**Общий параметр.** Используйте этот переключатель для тестирования и проверки параметров ввода без фактического включения мониторинга.

## <a name="output"></a>Выходные данные


#### <a name="example-output-from-a-successful-enablement"></a>Пример выхода из успешного включения

```
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

## <a name="next-steps"></a>Следующие шаги

  Просмотр телеметрии:
 - [Изучите метрики](../../azure-monitor/platform/metrics-charts.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Используйте Аналитику](../../azure-monitor/app/analytics.md) для более продвинутых запросов.
- [Создание панелей мониторинга.](../../azure-monitor/app/overview-dashboard.md)
 
 Добавление данных телеметрии:
 - [Создайте веб-тесты](monitor-web-app-availability.md), чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента,](../../azure-monitor/app/javascript.md) чтобы увидеть исключения из кода веб-страницы и включить отслеживание вызовов.
- [Добавьте SDK Application Insights в свой код,](../../azure-monitor/app/asp-net.md) чтобы вы могли вставлять вызовы трассировки и регистрации.
 
 Сделайте больше с агентом По анализу приложений:
 - Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
 - [Получите конфигурацию,](status-monitor-v2-api-get-config.md) чтобы подтвердить, что ваши настройки были записаны правильно.
 - [Получите статус](status-monitor-v2-api-get-status.md) для проверки мониторинга.
