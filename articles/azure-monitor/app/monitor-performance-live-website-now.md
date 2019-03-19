---
title: Мониторинг активного веб-приложения ASP.NET с помощью Azure Application Insights | Документация Майкрософт
description: Мониторинг производительности веб-сайта без необходимости его повторного развертывания. Работает с веб-приложениями ASP.NET, размещенными локально или в виртуальных машинах.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 0587782cbfa31f7b397b950a752040cc678cf7d7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085819"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-status-monitor"></a>Инструментирование веб-приложений во время выполнения с помощью монитора состояний Application Insights

Действующее веб-приложение можно инструментировать с помощью Azure Application Insights, не прибегая к изменению или повторному развертыванию кода. Вам потребуется подписка [Microsoft Azure](https://azure.com) .

Монитор состояний используется для инструментирования приложения .NET, размещенного в IIS (локально или на виртуальной машине).

- Если ваше приложение развернуто в службах приложений Azure, выполните [эти инструкции](azure-web-apps.md).
- Если приложение развернуто на виртуальной машине Azure, вы можете включить мониторинг Application Insights на панели управления Azure.
- (Существуют отдельные статьи об инструментировании [облачных служб Azure](../../azure-monitor/app/cloudservices.md).)


![Снимок экрана App Insights: графики, содержащие сведения о неудачных запросах, времени отклика сервера и запросов сервера](./media/monitor-performance-live-website-now/overview-graphs.png)

Вы можете выбрать один из двух указанных ниже вариантов применения Application Insights для веб-приложений .NET.

* **Во время сборки.** [Добавьте пакет SDK для Application Insights][greenbrown] в код своего веб-приложения.
* **Во время выполнения**. Инструментируйте веб-приложение на сервере, как описано ниже, без повторной сборки и развертывания кода.

> [!NOTE]
> Если вы используете инструментирование времени сборки, инструментирование времени выполнения не будет работать, даже если оно включено.

Ниже представлено общее сравнение предлагаемых вариантов.

|  | Во время сборки | Во время выполнения |
| --- | --- | --- |
| Запросы и исключения |Yes |Yes |
| [Более подробные исключения](../../azure-monitor/app/asp-net-exceptions.md) | |Yes |
| [Диагностика зависимостей](../../azure-monitor/app/asp-net-dependencies.md) |На платформе .NET 4.6 или более поздней, неполные сведения |Да, полные сведения: коды результатов, текст команд SQL, HTTP-команда|
| [Счетчики производительности системы](../../azure-monitor/app/performance-counters.md) |Yes |Yes |
| [API для пользовательской телеметрии][api] |Yes |Нет  |
| [Интеграция журнала трассировки](../../azure-monitor/app/asp-net-trace-logs.md) |Yes |Нет  |
| [Просмотр страницы и пользовательские данные](../../azure-monitor/app/javascript.md) |Yes |Нет  |
| Требуется повторная сборка кода |Yes | Нет  |



## <a name="monitor-a-live-iis-web-app"></a>Мониторинг активного веб-приложения IIS

Если приложение размещено на сервере IIS, включите Application Insights с помощью монитора состояний.

1. Войдите на веб-сервер IIS с учетными данными администратора.
2. Если монитор состояний Application Insights еще не установлен, [скачайте и запустите установщик](#download).
3. В мониторе состояний выберите установленное веб-приложение или веб-сайт, который требуется отслеживать. Выполните вход с использованием учетных данных Azure.

    Настройте ресурс, в котором должны отображаться результаты на портале Application Insights. (Как правило, проще всего создать ресурс. Выберите имеющийся ресурс, если у вас уже есть [веб-тесты][availability] или [наблюдение за клиентами][client] для этого приложения.) 

    ![Выберите приложение и ресурс.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Перезапустите IIS.

    ![Выберите "Перезапустить" в верхней части диалогового окна.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Работа вашей веб-службы будет ненадолго прервана.

## <a name="customize-monitoring-options"></a>Настройка параметров мониторинга

Если включить Application Insights, в веб-приложение будут добавлены библиотеки DLL и файл ApplicationInsights.config. Вы можете [отредактировать CONFIG-файл](../../azure-monitor/app/configuration-with-applicationinsights-config.md), чтобы изменить некоторые параметры.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>При повторной публикации приложения необходимо повторно включить Application Insights

Прежде чем повторно опубликовать приложение, необходимо [добавить Application Insights в код в Visual Studio][greenbrown]. Вы получите более подробные данные телеметрии и возможность написать пользовательскую телеметрию.

Если вы хотите повторно опубликовать приложение, не добавляя Application Insights в код, имейте в виду, что в процессе развертывания библиотеки DLL и файл ApplicationInsights.config могут быть удалены из опубликованного веб-сайта. Таким образом:

1. При редактировании файла ApplicationInsights.config сделайте его копию, прежде чем повторно опубликовать приложение.
2. Повторно опубликуйте приложение.
3. Повторно включите мониторинг Application Insights. Используйте подходящий способ: панель управления веб-приложением Azure или монитор состояния на узле IIS.
4. Восстановите изменения, внесенные в CONFIG-файл.


## <a name="troubleshoot"></a>Устранение неполадок

### <a name="confirm-a-valid-installation"></a>Подтверждение правильности установки 

Ниже приведены некоторые действия, которые вы можете выполнить, чтобы убедиться в успешности установки.

- Убедитесь, что файл applicationinsights.config находится в целевом каталоге приложения и содержит ikey.

- Если вы подозреваете, что данные отсутствуют, выполните простой запрос в [Analytics](../log-query/get-started-portal.md), чтобы перечислить все облачные роли, которые в данный момент отсылают данные телеметрии.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Если вам нужно подтвердить, что Application Insights успешно подключен, вы можете запустить программу [Sysinternals Handle](https://docs.microsoft.com/sysinternals/downloads/handle) в командном окне, чтобы подтвердить, что файл applicationinsights.dll был загружен службами IIS.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Проблемы с подключением? Отсутствие данных телеметрии

* Чтобы монитор состояний работал, в брандмауэре сервера необходимо открыть [некоторые исходящие порты](../../azure-monitor/app/ip-addresses.md#outgoing-ports) .

### <a name="unable-to-login"></a>Не удалось войти в систему

* Если монитор состояний не может войти в систему, установите командную строку. Монитор состояний пытается войти в систему для сбора ключа ikey, однако его можно указать вручную с помощью команды:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Не удалось загрузить файл или сборку System.Diagnostics.DiagnosticSource

Данная ошибка может возникать после включения Application Insights. Это связано с тем, что программа установки заменяет эту библиотеку DLL в вашем каталоге bin.
Чтобы обновить файл web.config, внеся в него исправления:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Мы отслеживаем эту проблему [здесь](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Диагностические сообщения приложения

* Откройте монитор состояний и на левой панели выберите свое приложение. Проверьте, не появились ли в разделе "Уведомления о конфигурации» сообщения с диагностическими сведениями для этого приложения:

  ![Откройте колонку "Производительность", чтобы отобразить сведения о запросе, времени отклика, зависимости и другие данные.](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Подробные журналы

* По умолчанию монитор состояний будет выводить диагностические журналы в `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`.

* Чтобы выводить подробные журналы, измените файл конфигурации `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` и добавьте `<add key="TraceLevel" value="All" />` для `appsettings`.
Затем перезапустите монитор состояний.

### <a name="insufficient-permissions"></a>Недостаточные разрешения
  
* Если на сервере отображается сообщение "Недостаточно разрешений", выполните следующее:
  * В диспетчере IIS выберите свой пул приложений, откройте **Дополнительные параметры** и в разделе **Модель процесса** скопируйте значение параметра "Идентификация".
  * На панели управления компьютера добавьте это значение к группе пользователей монитора производительности.

### <a name="conflict-with-systems-center-operations-manager"></a>Конфликт с Systems Center Operations Manager

* Если на вашем сервере установлен MMA/SCOM (Systems Center Operations Manager), некоторые версии могут конфликтовать. Удалите SCOM и монитор состояний и повторно установите последние версии.

### <a name="failed-or-incomplete-installation"></a>Неудачная или неполная установка

Если во время установки происходит сбой монитора состояний, вы можете остаться с неполной установкой, из которой монитор состояний не сможет восстановиться. В результате потребуется выполнить сброс вручную.

Удалите следующие файлы, найденные в каталоге приложения:
- любые библиотеки DLL в каталоге bin, начинающиеся с Microsoft.AI либо с Microsoft.ApplicationInsights;
- библиотеку DLL Microsoft.Web.Infrastructure.dll в каталоге bin;
- библиотеку DLL System.Diagnostics.DiagnosticSource.dll в каталоге bin;
- в каталоге приложения удалите App_Data\packages;
- в каталоге приложения удалите файл applicationinsights.config.


### <a name="additional-troubleshooting"></a>Дополнительные сведения об устранении неполадок

* Ознакомьтесь с дополнительными [сведениями об устранении неполадок][qna].

## <a name="system-requirements"></a>Требования к системе
Операционные системы, которые поддерживаются для монитора состояний Application Insights на сервере:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2.
* Windows Server 2016

На них должны быть установлены последний пакет обновления и платформа .NET Framework 4.5.

На клиентских компьютерах: Windows 7, 8, 8.1 или 10 с платформой .NET Framework 4.5.

Поддержка IIS: IIS 7, 7.5, 8, 8.5 (требуются службы IIS).

## <a name="automation-with-powershell"></a>Автоматизация с помощью PowerShell
Мониторинг можно запускать и останавливать с помощью PowerShell на сервере IIS.

Сначала импортируйте модуль Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Узнайте, какие приложения отслеживаются:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (необязательный параметр) — имя веб-приложения.
* Отображает состояние мониторинга Application Insights для каждого веб-приложения (или именованного приложения) на этом сервере IIS.
* Возвращает `ApplicationInsightsApplication` для каждого приложения.

  * `SdkState==EnabledAfterDeployment`: Приложение отслеживается. Оно инструментировано во время выполнения с помощью монитора состояний или командлета `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: Приложение не инструментировано для Application Insights. Оно либо никогда не было инструментировано, либо мониторинг во время выполнения был отключен с помощью монитора состояния или командлета `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: Приложение инструментировано путем добавления пакета SDK в исходный код. Этот пакет SDK нельзя обновить или остановить.
  * `SdkVersion` — отображает версию, которая используется для мониторинга этого приложения.
  * `LatestAvailableSdkVersion`— отображает версию, доступную сейчас в коллекции NuGet. Чтобы обновить приложение до этой версии, используйте командлет `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` — имя приложения на сервере IIS.
* `-InstrumentationKey` — ключ инструментирования ресурса Application Insights, в котором должны отображаться результаты.
* Этот командлет влияет только на приложения, которые еще не инструментированы, то есть SdkState==NotInstrumented.

    Командлет не влияет на приложение, которое уже инструментировано. Не имеет значения, инструментировано ли приложение во время сборки при добавлении пакета SDK в код или с использованием этого же командлета ранее во время выполнения.

    Версия пакета SDK для инструментирования приложения — это последняя версия, загруженная на этот сервер.

    Чтобы загрузить последнюю версию, используйте командлет Update-ApplicationInsightsVersion.
* В случае успешного выполнения возвращает `ApplicationInsightsApplication` . В случае сбоя трассировка записывается в stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` — имя приложения на сервере IIS.
* `-All` — останавливает мониторинг всех приложений на этом сервере IIS, для которых `SdkState==EnabledAfterDeployment`.
* Останавливает мониторинг для указанных приложений и удаляет инструментирование. Применимо только для приложений, инструментированных во время выполнения с использованием монитора состояния или командлета Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Возвращает ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Имя веб-приложения на сервере IIS.
* `-InstrumentationKey` (необязательный параметр). используется для изменения ресурса, в который отправляются данные телеметрии приложения.
* Этот командлет:
  * Обновляет именованное приложение до последней версии пакета SDK, загруженной на этот компьютер (работает, только если `SdkState==EnabledAfterDeployment`).
  * Если указан ключ инструментирования, именованное приложение повторно настраивается для отправки данных телеметрии в ресурс с этим ключом (работает, если `SdkState != Disabled`).

`Update-ApplicationInsightsVersion`

* Загружает последний пакет SDK для Application Insights на сервер.

## <a name="questions"></a>Вопросы о мониторе состояния

### <a name="what-is-status-monitor"></a>Что такое монитор состояния?

Классическое приложение, установленное на веб-сервер IIS. Оно позволяет инструментировать и настраивать веб-приложения. 

### <a name="when-do-i-use-status-monitor"></a>В каких случаях нужно использовать монитор состояния?

* Для инструментирования любого веб-приложения на сервере IIS (даже если оно уже запущено).
* Чтобы включить дополнительные данные телеметрии для веб-приложений, [созданных с помощью пакета SDK для Application Insights](../../azure-monitor/app/asp-net.md) во время компиляции. 

### <a name="can-i-close-it-after-it-runs"></a>Можно ли его закрыть после выполнения?

Да. После инструментирования нужных веб-сайтов его можно закрыть.

Монитор состояния не собирает данные телеметрии самостоятельно. Он лишь настраивает веб-приложения и устанавливает некоторые разрешения.

### <a name="what-does-status-monitor-do"></a>Как работает монитор состояния?

При выборе веб-приложения для инструментирования монитор состояния делает следующее:

* Скачивает и помещает сборки Application Insights и файл ApplicationInsights.config в папку с двоичными файлами веб-приложений.
* Включает профилирование среды CLR для сбора вызовов зависимостей.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Какую версию пакета SDK Application Insights устанавливает монитор состояний?

Сейчас монитор состояний устанавливает только пакет SDK Application Insights версии 2.3 или 2.4.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Нужно ли запускать монитор состояния при каждом обновлении приложения?

Нет, если повторное развертывание выполняется поэтапно. 

Если в процессе публикации вы выбрали параметр Delete existing files (Удалить существующие файлы), для настройки Application Insights необходимо перезапустить монитор состояния.

### <a name="what-telemetry-is-collected"></a>Какие данные телеметрии собираются?

Для приложений, инструментированных во время выполнения с использованием монитора состояния:

* HTTP-запросы;
* вызовы зависимостей;
* Исключения
* Счетчики производительности

Для уже инструментированных приложений во время компиляции:

 * счетчики процессов;
 * вызовы зависимостей (.NET 4.5) и возвращаемые значения в вызовах зависимостей (.NET 4.6);
 * значения трассировки стека исключений.

[Подробнее](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download"></a>Скачивание монитора состояний

- Скачайте и запустите [установщик монитора состояний](https://go.microsoft.com/fwlink/?LinkId=506648).
- Как альтернативный вариант, запустите [установщик веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx) и найдите в нем монитор состояний Application Insights.

## <a name="next"></a>Дальнейшие действия

Просмотр телеметрии:

* [Изучите метрики](../../azure-monitor/app/metrics-explorer.md), чтобы отслеживать производительность и использование.
* [Выполняйте поиск событий и журналов][diagnostic] для диагностики неполадок.
* [Аналитика](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
* [Создайте панели мониторинга](../../azure-monitor/app/app-insights-dashboards.md)

Добавление данных телеметрии:

* [Создайте веб-тесты][availability], чтобы убедиться, что ваш сайт продолжает работать.
* [Добавьте телеметрию веб-клиента][usage], чтобы просматривать исключения в коде веб-страницы и вставлять вызовы трассировки.
* [Добавьте пакет SDK для Application Insights в код][greenbrown], чтобы иметь возможность вставлять вызовы трассировки и журналов.

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
