---
title: 'Справочник по API v2 монитор состояния Azure: Установить настройки | Документация Майкрософт'
description: Набор ссылок состояние монитора v2 API-ApplicationInsightsMonitoringConfig. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: 2ca738d5d79fc73f892922825d4b731e8ee92b72
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870497"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Состояние монитора v2 API: SET-ApplicationInsightsMonitoringConfig (v0.2.1-альфа-версия)

В этом документе описывается командлет, который поставляется как член [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>ОПИСАНИЕ

Задайте файл конфигурации без повторения выполняет полную переустановку. Перезапустите службы IIS, чтобы изменения вступили в силу.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с правами администратора.


## <a name="examples"></a>Примеры

### <a name="example-with-single-instrumentation-key"></a>Пример с один ключ
В этом примере все приложения на текущем компьютере будет назначен один ключ.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Пример с карты ключей инструментирования
В этом примере 
- `MachineFilter` будет соответствовать текущей машине с помощью `'.*'` подстановочный знак.
- `AppFilter='WebAppExclude'` предоставляет `null` InstrumentationKey. Это приложение не инструментировано.
- `AppFilter='WebAppOne'` будет назначить уникальный ключ инструментирования этого конкретного приложения.
- `AppFilter='WebAppTwo'` также будет назначить этого конкретного приложения уникальный ключ инструментирования.
- Наконец `AppFilter` также использует `'.*'` подстановочный знак для сопоставления всех веб-приложений не соответствует более ранних правилами сбора данных и назначает ключ инструментирования по умолчанию.
- Пробелы добавлены для удобства только для чтения.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Параметры 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Обязательный параметр.** Используйте этот параметр, чтобы указать один ключ инструментирования iKey для использования, все приложения на целевом компьютере.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Обязательный параметр.** Используйте этот параметр для предоставления несколько ключей инструментирования ikey и сопоставление приложения, которые следует использовать какие ikey. Можно создать сценарий отдельной установки для нескольких компьютеров, задав MachineFilter. 

> [!IMPORTANT] 
> Приложения будут сопоставляться с правил в порядке, в котором они предоставляются. Таким образом наиболее конкретные правила следует указать первым и последним наиболее общие правила.

#### <a name="schema"></a>Схема
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** приведено регулярное требуется c# имени компьютера или виртуальной машины.
    - ". *" будет соответствовать всем
    - «Имя компьютера» будет соответствовать только компьютеры с указанным именем.
- **AppFilter** приведено регулярное требуется c# имени компьютера или виртуальной машины.
    - ". *" будет соответствовать всем
    - «Имя_приложения» будет соответствовать только приложения служб IIS с указанным именем.
- **InstrumentationKey** необходим для включения мониторинга приложений, которые соответствуют выше два фильтра.
    - Оставьте это значение null, если вы хотите определить правила, чтобы исключить мониторинг


### <a name="-verbose"></a>-Verbose
**Общий параметр.** Этот параметр можно используйте для вывода подробных журналов.


## <a name="output"></a>Выход

Нет выходных данных по умолчанию.

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
