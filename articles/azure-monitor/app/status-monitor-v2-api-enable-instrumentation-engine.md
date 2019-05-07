---
title: 'Справочник по API v2 монитор состояния Azure: Включить инструментирование ядро | Документация Майкрософт'
description: Включить ссылку состояния монитора v2 API-InstrumentationEngine. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: d886aa364ca928d32100c570689f13beb0c682c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143414"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>Состояние монитора v2 API: Enable-InstrumentationEngine (v0.2.1-альфа-версия)

В этом документе описывается командлет, который поставляется как член [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>ОПИСАНИЕ

Этот командлет будет включить модуль инструментирования, задав некоторые разделы реестра.
Перезапустите службы IIS, чтобы эти изменения вступили в силу.

Ядро инструментирования можно дополнить данные, собранные пакеты SDK для .NET.
События и сообщения будут собраны, которые описывают выполнение управляемого процесса. Включая, но не ограничиваясь коды результатов зависимостей, HTTP-команды и текст команды SQL. 

Включите модуль инструментирования, если:
- Вы уже включили мониторинг с помощью командлета Enable, но не включали InstrumentationEngine.
- Вы вручную инструментировали приложение с помощью пакетов SDK для .NET и требуется для сбора дополнительных данных телеметрии.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с правами администратора.

> [!NOTE] 
> Этот командлет потребует просмотрите и примите наши лицензии и заявления о конфиденциальности.

> [!NOTE] 
> Модуль инструментирования требует дополнительных затрат и отключен по умолчанию.

## <a name="examples"></a>Примеры

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Параметры 

### <a name="-acceptlicense"></a>-AcceptLicense
**Необязательный параметр.** Этот параметр можно используйте для принятия лицензии и заявлением о конфиденциальности в автономных установок.

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Этот параметр можно используйте для вывода подробных журналов.

## <a name="output"></a>Выход


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Пример выходных данных из успешного включения ядро инструментирования

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
