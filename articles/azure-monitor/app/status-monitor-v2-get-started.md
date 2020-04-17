---
title: Azure Application Insights Agent - начало работы (ru) Документы Майкрософт
description: Руководство по быстрому запуску для агента по анализу приложений. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 4cfa136585611e81a4060c5544d5dc464b32f12c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537446"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Начало работы с Azure Monitor Application InsightS Agent для наемных серверов

Эта статья содержит команды быстрого запуска, которые, как ожидается, будут работать для большинства сред.
Инструкции зависят от PowerShell Gallery для распространения обновлений.
Эти команды поддерживают параметр PowerShell. `-Proxy`

Для объяснения этих команд, инструкций по настройке и информации об устранении неполадок смотрите [подробные инструкции.](status-monitor-v2-detailed-instructions.md)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="download-and-install-via-powershell-gallery"></a>Скачать и установить через powerShell Галерея

### <a name="install-prerequisites"></a>Установка необходимых компонентов
Запустите PowerShell в роли админа.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Закройте PowerShell.

### <a name="install-application-insights-agent"></a>Установка агента по анализу приложений
Запустите PowerShell в роли админа.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Включение мониторинга
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Скачать и установить вручную (оффлайн вариант)
### <a name="download-the-module"></a>Скачать модуль
Вручную загрузите последнюю версию модуля из [галереи PowerShell.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)

### <a name="unzip-and-install-application-insights-agent"></a>Unzip и установить приложение Исследования агент
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Включение мониторинга
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

- Просмотрите [подробные инструкции](status-monitor-v2-detailed-instructions.md) для объяснения команд, найденных здесь.
- Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
