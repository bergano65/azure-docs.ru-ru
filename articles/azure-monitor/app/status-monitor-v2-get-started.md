---
title: Azure версии 2 монитор состояния Приступая к работе | Документация Майкрософт
description: Краткое руководство для монитора состояния v2. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: c20cbf50129247fa01f10f12a99c558bc86492b8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145176"
---
# <a name="getting-started-with-status-monitor-v2"></a>Приступая к работе с v2 монитор состояния

Этот документ содержит примеры использования команд, должен работать для большинства сред. Эти инструкции зависят от коллекции PowerShell для распространения обновлений. Эти команды поддержки PowerShell `-Proxy` параметра.

Просмотрите наши [подробные инструкции](status-monitor-v2-detailed-instructions.md) страницы для объяснения этих команд, инструкции по настройке и устранении неполадок.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="download--install-via-powershell-gallery"></a>Скачивание и установка с помощью коллекции PowerShell

### <a name="install-prerequisites"></a>установить необходимые компоненты;
Запустите PowerShell от имени администратора
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Закройте PowerShell

### <a name="install-status-monitor-v2"></a>Состояние установки отслеживать версии 2
Запустите PowerShell от имени администратора
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Включить мониторинг
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>Загрузить и установить вручную (параметр-offline)
### <a name="manual-download"></a>Загрузка вручную
Вручную Загрузите последнюю версию модуля. https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>Распакуйте и установите монитор состояния v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.2.1-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Включить мониторинг
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

- Просмотрите [подробные инструкции](status-monitor-v2-detailed-instructions.md) описание команды, найденные в данном руководстве.
- Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
