---
title: Azure монитор состояния v2. Приступая к работе | Документация Майкрософт
description: Краткое руководство по монитор состояния v2. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
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
ms.openlocfilehash: d9c354edac3cbd3faccaa261654e56f858befdf6
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058237"
---
# <a name="get-started-with-status-monitor-v2"></a>Начало работы с монитором состояний версии 2

Эта статья содержит команды краткого руководства, которые должны работать в большинстве сред.
Инструкции зависят от коллекция PowerShell распространения обновлений.
Эти команды поддерживают параметр PowerShell `-Proxy` .

Описание этих команд, инструкции по настройке и сведения об устранении неполадок см. в [подробных инструкциях](status-monitor-v2-detailed-instructions.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="download-and-install-via-powershell-gallery"></a>Загрузка и установка с помощью коллекция PowerShell

### <a name="install-prerequisites"></a>установить необходимые компоненты;
Запустите PowerShell от имени администратора.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Закройте PowerShell.

### <a name="install-status-monitor-v2"></a>Установка монитор состояния v2
Запустите PowerShell от имени администратора.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Включение мониторинга
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Скачать и установить вручную (автономный режим)
### <a name="download-the-module"></a>Скачать модуль
Вручную Скачайте последнюю версию модуля из [коллекция PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Распакуйте и установите монитор состояния версии 2
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

- [Изучите метрики](../../azure-monitor/app/metrics-explorer.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- [Используйте аналитику](../../azure-monitor/app/analytics.md) для более сложных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).

 Добавление данных телеметрии:

- [Создайте веб-тесты](monitor-web-app-availability.md) , чтобы убедиться, что ваш сайт остается активным.
- [Добавьте данные телеметрии веб-клиента](../../azure-monitor/app/javascript.md) , чтобы просмотреть исключения из кода веб-страницы и включить вызовы трассировки.
- [Добавьте в код пакет SDK для Application Insights](../../azure-monitor/app/asp-net.md) , чтобы можно было вставить вызовы трассировки и журнала.

Дополнительные возможности монитор состояния версии 2:

- Ознакомьтесь с [подробными инструкциями](status-monitor-v2-detailed-instructions.md) для объяснения команд, найденных здесь.
- Воспользуйтесь нашим руководством по [устранению неполадок](status-monitor-v2-troubleshoot.md) монитор состояния v2.
