---
title: 'Справочник по API Azure монитор состояния v2: Включить мониторинг | Документация Майкрософт'
description: Справочник по API монитор состояния v2. Enable-Аппликатионинсигхтсмониторинг. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
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
ms.openlocfilehash: 8742316697e6d6c8178bb02c8e7288499c655b6b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033128"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring"></a>API монитор состояния v2: Enable-Аппликатионинсигхтсмониторинг

В этой статье описывается командлет, который является членом [модуля PowerShell AZ. аппликатионмонитор](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Включает некодированный мониторинг приложений IIS на конечном компьютере.

Этот командлет изменит IIS applicationHost. config и установит некоторые разделы реестра.
Кроме того, будет создан файл applicationinsights. iKey. config, который определяет ключ инструментирования, используемый каждым приложением.
Службы IIS загружают Редфиелдмодуле при запуске, что приводит к внедрению пакета SDK Application Insights в приложения при запуске приложений.
Перезапустите IIS, чтобы изменения вступили в силу.

После включения мониторинга рекомендуется использовать [динамические метрики](live-stream.md) , чтобы быстро проверить, отправляет ли ваше приложение данные телеметрии.


> [!NOTE] 
> - Чтобы приступить к работе, необходим ключ инструментирования. Дополнительные сведения см. [в разделе Создание ресурса](create-new-resource.md#copy-the-instrumentation-key).
> - Для этого командлета необходимо проверить и принять нашу лицензию и заявление о конфиденциальности.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора и политикой выполнения с повышенными правами. Дополнительные сведения см. в статье [Запуск PowerShell от имени администратора с повышенной политикой выполнения](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Примеры

### <a name="example-with-a-single-instrumentation-key"></a>Пример с одним ключом инструментирования
В этом примере всем приложениям на текущем компьютере назначается один ключ инструментирования.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Пример с картой ключа инструментирования
В данном примере:
- `MachineFilter`соответствует текущему компьютеру с помощью `'.*'` подстановочного знака.
- `AppFilter='WebAppExclude'`предоставляет ключ `null` инструментирования. Указанное приложение не будет инструментировано.
- `AppFilter='WebAppOne'`Присваивает указанному приложению уникальный ключ инструментирования.
- `AppFilter='WebAppTwo'`Присваивает указанному приложению уникальный ключ инструментирования.
- Наконец, `AppFilter` также `'.*'` использует подстановочный знак для сопоставления всех веб-приложений, которые не соответствуют предыдущим правилам, и назначения ключа инструментирования по умолчанию.
- Для удобочитаемости добавляются пробелы.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Параметры

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Обязательный параметр.** Используйте этот параметр, чтобы указать один ключ инструментирования для использования всеми приложениями на целевом компьютере.

### <a name="-instrumentationkeymap"></a>-Инструментатионкэймап
**Обязательный параметр.** Этот параметр используется для предоставления нескольких ключей инструментирования и сопоставления ключей инструментирования, используемых каждым приложением.
Можно создать один скрипт установки для нескольких компьютеров, установив `MachineFilter`.

> [!IMPORTANT]
> Приложения будут соответствовать правилам в том порядке, в котором предоставляются правила. Поэтому необходимо сначала указать наиболее конкретные правила, а также наиболее общие правила.

#### <a name="schema"></a>Схема
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **Мачинефилтер** — это обязательное C# регулярное выражение имени компьютера или виртуальной машины.
    - ". *" будет соответствовать всем
    - "ComputerName" будет соответствовать только компьютерам с указанным именем.
- **Аппфилтер** — это обязательное C# регулярное выражение имени веб-сайта IIS. Список сайтов на сервере можно получить, выполнив команду [Get-ииссите](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - ". *" будет соответствовать всем
    - "SiteName" будет соответствовать только сайту IIS с указанным точным именем.
- **InstrumentationKey** требуется для включения мониторинга приложений, соответствующих двум предыдущим фильтрам.
    - Если необходимо определить правила для исключения мониторинга, оставьте значение null.


### <a name="-enableinstrumentationengine"></a>-Енаблеинструментатионенгине
**Необязательный параметр.** Используйте этот параметр, чтобы модуль инструментирования собирал события и сообщения о том, что происходит во время выполнения управляемого процесса. Эти события и сообщения включают в себя коды результатов зависимостей, глаголы HTTP и текст команды SQL.

Модуль инструментирования добавляет издержки и по умолчанию отключен.

### <a name="-acceptlicense"></a>-AcceptLicense
**Необязательный параметр.** Используйте этот параметр, чтобы принять условия лицензии и конфиденциальности в установках без монитора.

### <a name="-ignoresharedconfig"></a>-Игнорешаредконфиг
При наличии кластера веб-серверов можно использовать [общую конфигурацию](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Не удается внедрить HttpModule в эту общую конфигурацию.
Этот сценарий завершится ошибкой с сообщением о необходимости дополнительных шагов установки.
Используйте этот параметр, чтобы пропустить эту проверку и продолжить установку необходимых компонентов. Дополнительные сведения см. в разделе [Известные конфликты — с-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration) .

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот параметр для просмотра подробных журналов.

### <a name="-whatif"></a>-WhatIf 
**Общий параметр.** Используйте этот переключатель для проверки и проверки входных параметров без фактического включения мониторинга.

## <a name="output"></a>Вывод


#### <a name="example-output-from-a-successful-enablement"></a>Пример выходных данных успешного включения

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
 - [Изучите метрики](../../azure-monitor/app/metrics-explorer.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Используйте аналитику](../../azure-monitor/app/analytics.md) для более сложных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).
 
 Добавление данных телеметрии:
 - [Создайте веб-тесты](monitor-web-app-availability.md) , чтобы убедиться, что ваш сайт остается активным.
- [Добавьте данные телеметрии веб-клиента](../../azure-monitor/app/javascript.md) , чтобы просмотреть исключения из кода веб-страницы и включить вызовы трассировки.
- [Добавьте в код пакет SDK для Application Insights](../../azure-monitor/app/asp-net.md) , чтобы можно было вставить вызовы трассировки и журнала.
 
 Дополнительные возможности монитор состояния версии 2:
 - Воспользуйтесь нашим руководством по [устранению неполадок](status-monitor-v2-troubleshoot.md) монитор состояния v2.
 - [Получите конфигурацию](status-monitor-v2-api-get-config.md) , чтобы убедиться, что параметры записаны правильно.
 - [Получение состояния](status-monitor-v2-api-get-status.md) для проверки мониторинга.
