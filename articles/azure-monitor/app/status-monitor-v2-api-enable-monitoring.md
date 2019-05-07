---
title: 'Справочник по API v2 монитор состояния Azure: Включить мониторинг | Документация Майкрософт'
description: Включить ссылку состояния монитора v2 API-ApplicationInsightsMonitoring. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: 17798c78b167821f5f30a11996ac90cf67fb0179
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144947"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>Состояние монитора v2 API: Enable-ApplicationInsightsMonitoring (v0.2.1-альфа-версия)

В этом документе описывается командлет, который поставляется как член [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>ОПИСАНИЕ

Включение кода без участия присоединения, мониторинг приложений IIS на целевом компьютере.
Этот командлет будет изменять IIS applicationHost.config и задать некоторые разделы реестра.
Этот командлет создаст applicationinsights.ikey.config, который определяет, какой ключ инструментирования используется какое приложение.
IIS будет загружать RedfieldModule во время запуска будет внедрять пакет SDK Application Insights в приложения, как запуск этих приложений.
Перезапустите службы IIS, чтобы изменения вступили в силу.

После включения мониторинга, мы рекомендуем использовать [Live Metrics](live-stream.md) Чтобы быстро просматривать, если приложение может отправлять нам данные телеметрии.


> [!NOTE] 
> Чтобы приступить к работе, необходимо иметь ключ инструментирования. Дополнительные сведения в статье [создать новый ресурс](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с правами администратора и с политикой выполнение с повышенными правами. Чтение [здесь](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) Дополнительные сведения.

> [!NOTE] 
> Этот командлет потребует просмотрите и примите наши лицензии и заявления о конфиденциальности.


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


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Необязательный параметр.** Используйте этот параметр, чтобы включить ядро инструментирования для сбора событий и сообщений, что происходит во время выполнения управляемого процесса. Включая, но не ограничиваясь коды результатов зависимостей, HTTP-команды и текст команды SQL. Модуль инструментирования будет привести к дополнительной нагрузке и по умолчанию отключены.

### <a name="-acceptlicense"></a>-AcceptLicense
**Необязательный параметр.** Этот параметр можно используйте для принятия лицензии и заявлением о конфиденциальности в автономных установок.

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Этот параметр можно используйте для вывода подробных журналов.

### <a name="-whatif"></a>-WhatIf 
**Общий параметр.** Этот параметр можно используйте для тестирования и проверки входных параметров без фактически Включение мониторинга.

## <a name="output"></a>Выход


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

## <a name="next-steps"></a>Дальнейшие действия

  Просмотр телеметрии:
 - [Изучите метрики](../../azure-monitor/app/metrics-explorer.md), чтобы отслеживать производительность и использование.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем
- [Аналитика](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
- [Создайте панели мониторинга](../../azure-monitor/app/app-insights-dashboards.md)
 
 Добавление данных телеметрии:
 - [Создание веб-тестов](monitor-web-app-availability.md) чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента](../../azure-monitor/app/javascript.md) чтобы просматривать исключения в коде веб-страницы и вставлять вызовы трассировки.
- [Добавьте пакет SDK Application Insights в код](../../azure-monitor/app/asp-net.md) , можно вставить трассировки и журналов вызовов
 
 Новые возможности в версии 2 монитор состояния:
 - Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
 - [Получить конфигурацию](status-monitor-v2-api-get-config.md) для подтверждения того, что параметры были записаны неправильно.
 - [Получить состояние](status-monitor-v2-api-get-status.md) для проверки наблюдения.
