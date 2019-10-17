---
title: 'Справочник по API агента Azure Application Insights: set config | Документация Майкрософт'
description: Справочник по API агента Application Insights. Set-Аппликатионинсигхтсмониторингконфиг. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 2ab941b5587a8836f1e472fbce3966b12bfa1e11
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388255"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>API агента Application Insights: Set-Аппликатионинсигхтсмониторингконфиг

В этом документе описывается командлет, который является членом [модуля PowerShell AZ. аппликатионмонитор](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Задает файл конфигурации без выполнения полной переустановки.
Перезапустите IIS, чтобы изменения вступили в силу.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.


## <a name="examples"></a>Примеры

### <a name="example-with-a-single-instrumentation-key"></a>Пример с одним ключом инструментирования
В этом примере всем приложениям на текущем компьютере будет назначен один ключ инструментирования.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Пример с картой ключа инструментирования
В данном примере:
- `MachineFilter` соответствует текущему компьютеру с помощью подстановочного знака `'.*'`.
- `AppFilter='WebAppExclude'` предоставляет ключ инструментирования `null`. Указанное приложение не будет инструментировано.
- `AppFilter='WebAppOne'` назначает заданному приложению уникальный ключ инструментирования.
- `AppFilter='WebAppTwo'` назначает заданному приложению уникальный ключ инструментирования.
- Наконец, `AppFilter` также использует шаблон `'.*'`, чтобы сопоставить все веб-приложения, которые не соответствуют предыдущим правилам, и назначить ключ инструментирования по умолчанию.
- Для удобочитаемости добавляются пробелы.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Параметры

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Обязательный параметр.** Используйте этот параметр, чтобы указать один ключ инструментирования для использования всеми приложениями на целевом компьютере.

### <a name="-instrumentationkeymap"></a>-Инструментатионкэймап
**Обязательный параметр.** Этот параметр используется для предоставления нескольких ключей инструментирования и сопоставления ключей инструментирования, используемых каждым приложением.
Можно создать один скрипт установки для нескольких компьютеров, установив значение `MachineFilter`.

> [!IMPORTANT]
> Приложения будут соответствовать правилам в том порядке, в котором предоставляются правила. Поэтому необходимо сначала указать наиболее конкретные правила, а также наиболее общие правила.

#### <a name="schema"></a>SCHEMA (Схема)
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **Мачинефилтер** — это обязательное C# регулярное выражение имени компьютера или виртуальной машины.
    - ". *" будет соответствовать всем
    - "ComputerName" будет соответствовать только компьютерам с указанным именем.
- **Аппфилтер** — это обязательное C# регулярное выражение имени компьютера или виртуальной машины.
    - ". *" будет соответствовать всем
    - "ApplicationName" будет сопоставлять только приложения IIS с указанным именем.
- **InstrumentationKey** требуется для включения мониторинга приложений, соответствующих двум предыдущим фильтрам.
    - Если необходимо определить правила для исключения мониторинга, оставьте значение null.


### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот параметр для просмотра подробных журналов.


## <a name="output"></a>Выходные данные

По умолчанию выходные данные отсутствуют.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Пример подробных данных о настройке файла конфигурации с помощью-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Пример подробных данных о настройке файла конфигурации с помощью-Инструментатионкэймап

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
- [Используйте аналитику](../../azure-monitor/app/analytics.md) для более сложных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).
 
 Добавление данных телеметрии:
 - [Создайте веб-тесты](monitor-web-app-availability.md) , чтобы убедиться, что ваш сайт остается активным.
- [Добавьте данные телеметрии веб-клиента](../../azure-monitor/app/javascript.md) , чтобы просмотреть исключения из кода веб-страницы и включить вызовы трассировки.
- [Добавьте в код пакет SDK для Application Insights](../../azure-monitor/app/asp-net.md) , чтобы можно было вставлять вызовы трассировки и журнала.
 
 Другие действия с агентом Application Insights:
 - Используйте наше справочное по для [устранения неполадок](status-monitor-v2-troubleshoot.md) агента Application Insights.
 - [Получите конфигурацию](status-monitor-v2-api-get-config.md) , чтобы убедиться, что параметры записаны правильно.
 - [Получение состояния](status-monitor-v2-api-get-status.md) для проверки мониторинга.
