---
title: 'Справочник по API v2 монитор состояния Azure: Получить конфигурацию | Документация Майкрософт'
description: Get ссылку состояния монитора v2 API-ApplicationInsightsMonitoringConfig. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: 28893e0782a7fa928757bbbba42662e6d3fa272c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153585"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>Состояние монитора v2 API: Get-ApplicationInsightsMonitoringConfig (v0.2.1-альфа-версия)

В этом документе описывается командлет, который поставляется как член [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>ОПИСАНИЕ

Получите файл конфигурации и вывода значений на консоль.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с правами администратора.

## <a name="examples"></a>Примеры

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Параметры 

(Без параметров не требуется)

## <a name="output"></a>Выход


#### <a name="example-output-from-reading-the-config-file"></a>Пример выходных данных из чтения файла конфигурации

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
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
 - Вносить изменения в этой конфигурации с помощью [Set config](status-monitor-v2-api-set-config.md) командлета.
