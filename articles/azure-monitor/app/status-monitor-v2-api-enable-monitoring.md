---
title: 'Монитор состояния v2 Справочник по API Azure: Включить мониторинг | Документация Майкрософт'
description: Справочник по API v2 монитор состояния. Enable-ApplicationInsightsMonitoring. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложений ASP.NET, размещенным на предприятиях, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: 1e5a4ac9f159ce238149d44127fd63dcf3279623
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807103"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v040-alpha"></a>Состояние монитора v2 API: Enable-ApplicationInsightsMonitoring (v0.4.0-альфа-версия)

В этой статье описывается командлет, который является членом [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Описание

Предоставления без помощи кода позволяет подключить, мониторинг приложений IIS на конечном компьютере.

Этот командлет будет изменять IIS applicationHost.config и задать некоторые разделы реестра.
Он также создаст файл applicationinsights.ikey.config, который определяет ключ инструментирования, используемый каждого приложения.
IIS будет загружать RedfieldModule во время запуска, который обеспечивает включение Application Insights SDK в приложения, как запуск приложения.
Перезапустите службы IIS, чтобы изменения вступили в силу.

После включения мониторинга, мы рекомендуем использовать [Live Metrics](live-stream.md) для быстрой проверки, если ваше приложение может отправлять нам данные телеметрии.


> [!NOTE] 
> - Чтобы приступить к работе, вам потребуется ключ инструментирования. Дополнительные сведения см. в разделе [создать ресурс](create-new-resource.md#copy-the-instrumentation-key).
> - Для этого командлета требуется, просмотрите и примите наши лицензии и заявления о конфиденциальности.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора и политику выполнение с повышенными правами. Дополнительные сведения см. в разделе [запустите PowerShell от имени администратора с помощью политики выполнение с повышенными правами](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Примеры

### <a name="example-with-a-single-instrumentation-key"></a>Пример с один ключ
В этом примере все приложения на текущем компьютере назначаются один ключ.

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
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** требуется C# regex компьютера или имя виртуальной Машины.
    - ". *" будет соответствовать всем
    - «Имя компьютера» будет соответствовать только компьютеры с указанным именем точно.
- **AppFilter** требуется C# регулярное выражение имени узла IIS. Список сайтов на сервере можно получить, выполнив команду [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - ". *" будет соответствовать всем
    - «SiteName» будет соответствовать только сайт IIS с указанным именем точно.
- **InstrumentationKey** необходим для включения мониторинга приложений, которые соответствуют выше два фильтра.
    - Оставьте это значение null, если вы хотите определить правила, чтобы исключить мониторинг.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Необязательный параметр.** Используйте этот параметр, чтобы включить ядро инструментирования для сбора событий и сообщений о происходящем во время выполнения управляемого процесса. Эти события и сообщения включают коды результатов зависимостей, HTTP-команды и текст команды SQL.

Модуль инструментирования добавляет издержки и отключен по умолчанию.

### <a name="-acceptlicense"></a>-AcceptLicense
**Необязательный параметр.** Этот параметр можно используйте для принятия лицензии и заявлением о конфиденциальности в автономных установок.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Если у вас есть кластер из веб-серверов, возможно, вы используете [общей конфигурации](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Модуль HttpModule не могут быть добавлены в этой общей конфигурации.
Этот сценарий завершится ошибкой с сообщением, что дополнительная установка действия являются обязательными.
Этот параметр следует Используйте эту проверку и продолжить Установка необходимых компонентов. Дополнительные сведения см. в разделе [известных конфликтов с iis общей конфигурации](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Этот параметр можно используйте для отображения подробных журналов.

### <a name="-whatif"></a>-WhatIf 
**Общий параметр.** Этот параметр можно используйте для тестирования и проверки входных параметров без фактически Включение мониторинга.

## <a name="output"></a>Вывод


#### <a name="example-output-from-a-successful-enablement"></a>Пример выходных данных из успешного подключения

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
 - [Изучение метрик](../../azure-monitor/app/metrics-explorer.md) для контроля производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Использование аналитики](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).
 
 Добавление данных телеметрии:
 - [Создание веб-тестов](monitor-web-app-availability.md) чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента](../../azure-monitor/app/javascript.md) чтобы просматривать исключения в коде веб-страницы и включить вызовы трассировки.
- [Добавьте пакет SDK Application Insights в код](../../azure-monitor/app/asp-net.md) чтобы можно было вставить трассировки и журналов вызовов.
 
 Новые возможности в версии 2 монитор состояния:
 - Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
 - [Получить конфигурацию](status-monitor-v2-api-get-config.md) для подтверждения того, что параметры были записаны неправильно.
 - [Получить состояние](status-monitor-v2-api-get-status.md) для проверки наблюдения.
