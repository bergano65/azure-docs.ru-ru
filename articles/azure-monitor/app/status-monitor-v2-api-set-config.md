---
title: Ссылка на API агентства API для анализа приложений приложений Azure
description: Ссылка на API агента приложений. Set-ApplicationInsightsMonitoringConfig. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1226b3e10adf786ed3335844a5d3f4e530911705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671245"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Приложение Insights Агент API: Set-ApplicationInsightsMonitoringConfig

В этом документе описывается cmdlet, который является членом [модуля Az.ApplicationMonitor PowerShell.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Описание

Устанавливает файл конфигурации, не делая полной переустановки.
Перезапустите IIS для вхастывания изменений.

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin.


## <a name="examples"></a>Примеры

### <a name="example-with-a-single-instrumentation-key"></a>Пример с одним ключом приборов
В этом примере всем приложениям на текущем компьютере будет назначен единый ключ прибора.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Пример с картой ключей приборов
В этом примере:
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

## <a name="parameters"></a>Параметры

### <a name="-instrumentationkey"></a>-ИнструментацияКей
**Обязательно.** Используйте этот параметр для предоставления единого ключа приборов для использования всеми приложениями на целевом компьютере.

### <a name="-instrumentationkeymap"></a>-ИнструментацияКейМап
**Обязательно.** Используйте этот параметр для предоставления нескольких ключей приборов и отображения ключей приборов, используемых каждым приложением.
Вы можете создать единый сценарий `MachineFilter`установки для нескольких компьютеров, установив.

> [!IMPORTANT]
> Приложения будут соответствовать правилам в порядке предоставления правил. Таким образом, вы должны указать наиболее конкретные правила первого и наиболее общие правила последнего.

#### <a name="schema"></a>схема
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** является обязательным регистратором компьютера или VM-именем.
    - '.' будет соответствовать всем
    - 'ComputerName' будет сопоставлять только компьютеры с указанным именем.
- **AppFilter** является обязательным регистратором компьютера или VM-именем.
    - '.' будет соответствовать всем
    - 'ApplicationName' будет соответствовать только приложениям IIS с указанным именем.
- **InstrumentationKey** требуется для обеспечения мониторинга приложений, которые соответствуют предыдущим двум фильтрам.
    - Оставьте это значение недействительным, если вы хотите определить правила, чтобы исключить мониторинг.


### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для отображения подробных журналов.


## <a name="output"></a>Выходные данные

По умолчанию, нет вывода.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Пример многословного вывода от установки конфигурационного файла через -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Пример многословного вывода от установки конфигурационного файла через -InstrumentationKeyMap

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

## <a name="next-steps"></a>Дальнейшие действия

  Просмотр телеметрии:
 - [Изучите метрики](../../azure-monitor/app/metrics-explorer.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Используйте Аналитику](../../azure-monitor/app/analytics.md) для более продвинутых запросов.
- [Создание панелей мониторинга.](../../azure-monitor/app/overview-dashboard.md)
 
 Добавление данных телеметрии:
 - [Создайте веб-тесты](monitor-web-app-availability.md), чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента,](../../azure-monitor/app/javascript.md) чтобы увидеть исключения из кода веб-страницы и включить отслеживание вызовов.
- [Добавьте SDK Application Insights в свой код,](../../azure-monitor/app/asp-net.md) чтобы вы могли вставлять вызовы трассий и журналов
 
 Сделайте больше с агентом По анализу приложений:
 - Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
 - [Получите конфигурацию,](status-monitor-v2-api-get-config.md) чтобы подтвердить, что ваши настройки были записаны правильно.
 - [Получите статус](status-monitor-v2-api-get-status.md) для проверки мониторинга.
