---
title: 'Монитор состояния v2 Справочник по API Azure: Установить настройки | Документация Майкрософт'
description: Справочник по API v2 монитор состояния. SET-ApplicationInsightsMonitoringConfig. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложений ASP.NET, размещенным на предприятиях, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734991"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Состояние монитора v2 API: SET-ApplicationInsightsMonitoringConfig (v0.2.1-альфа-версия)

В этом документе описывается командлет, который является членом [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Описание

Задает файл конфигурации без выполнения полной повторной установки.
Перезапустите службы IIS, чтобы изменения вступили в силу.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.


## <a name="examples"></a>Примеры

### <a name="example-with-a-single-instrumentation-key"></a>Пример с один ключ
В этом примере все приложения на текущем компьютере будет назначен один ключ.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Пример с картой ключа инструментирования
В данном примере:
- `MachineFilter` совпадает с текущего компьютера с помощью `'.*'` подстановочный знак.
- `AppFilter='WebAppExclude'` предоставляет `null` ключ инструментирования. Указанное приложение не инструментировано.
- `AppFilter='WebAppOne'` назначает уникальный ключ инструментирования для указанного приложения.
- `AppFilter='WebAppTwo'` назначает уникальный ключ инструментирования для указанного приложения.
- Наконец `AppFilter` также использует `'.*'` подстановочный знак для сопоставления всех веб-приложений, которые не совпадают с правилами сбора данных более ранней и назначить ключ инструментирования по умолчанию.
- Для удобства чтения добавляются пробелы.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Параметры

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Обязательный параметр.** Используйте этот параметр, чтобы указать один ключ для использования, все приложения на целевом компьютере.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Обязательный параметр.** Используйте этот параметр для предоставления нескольких ключей инструментирования и сопоставления из ключей инструментирования, используемые каждым приложением.
Можно создать сценарий отдельной установки для нескольких компьютеров, задав `MachineFilter`.

> [!IMPORTANT]
> Приложения будут сопоставляться с правил в порядке, в котором предоставляются правила. Таким образом наиболее конкретные правила следует указать первым и последним наиболее общие правила.

#### <a name="schema"></a>Схема
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** требуется C# regex компьютера или имя виртуальной Машины.
    - ". *" будет соответствовать всем
    - «Имя компьютера» будет соответствовать только компьютеры с указанным именем.
- **AppFilter** требуется C# regex компьютера или имя виртуальной Машины.
    - ". *" будет соответствовать всем
    - «Имя_приложения» будет соответствовать только приложения служб IIS с указанным именем.
- **InstrumentationKey** необходим для включения мониторинга приложений, которые соответствуют выше два фильтра.
    - Оставьте это значение null, если вы хотите определить правила, чтобы исключить мониторинг.


### <a name="-verbose"></a>-Verbose
**Общий параметр.** Этот параметр можно используйте для отображения подробных журналов.


## <a name="output"></a>Выход

По умолчанию выходные данные.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Подробный пример выходных данных из файла конфигурации с помощью - InstrumentationKey параметр

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Подробный пример выходных данных из файла конфигурации с помощью - InstrumentationKeyMap параметр

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
 - [Изучение метрик](../../azure-monitor/app/metrics-explorer.md) для контроля производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Использование аналитики](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).
 
 Добавление данных телеметрии:
 - [Создание веб-тестов](monitor-web-app-availability.md) чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента](../../azure-monitor/app/javascript.md) чтобы просматривать исключения в коде веб-страницы и включить вызовы трассировки.
- [Добавьте пакет SDK Application Insights в код](../../azure-monitor/app/asp-net.md) чтобы можно было вставить трассировки и журналов вызовов
 
 Новые возможности в версии 2 монитор состояния:
 - Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
 - [Получить конфигурацию](status-monitor-v2-api-get-config.md) для подтверждения того, что параметры были записаны неправильно.
 - [Получить состояние](status-monitor-v2-api-get-status.md) для проверки наблюдения.
