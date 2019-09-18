---
title: 'Справочник по API Azure монитор состояния v2: Включить модуль инструментирования | Документация Майкрософт'
description: Справочник по API монитор состояния v2. Enable-Инструментатионенгине. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
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
ms.openlocfilehash: 3c0f4e015d3e01e86daaf101c15e16857540a520
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033142"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine"></a>API монитор состояния v2: Enable-Инструментатионенгине

В этой статье описывается командлет, который является членом [модуля PowerShell AZ. аппликатионмонитор](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Включает модуль инструментирования, настроив некоторые разделы реестра.
Перезапустите IIS, чтобы изменения вступили в силу.

Модуль инструментирования может дополнять данные, собираемые пакетами SDK для .NET.
Он собирает события и сообщения, описывающие выполнение управляемого процесса. Эти события и сообщения включают в себя коды результатов зависимостей, глаголы HTTP и [текст команды SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Включите модуль инструментирования, если:
- Вы уже включили наблюдение с помощью командлета Enable, но не включили модуль инструментирования.
- Вы вручную разрешили свое приложение с помощью пакетов SDK для .NET и хотите получить дополнительные данные телеметрии.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

> [!NOTE] 
> - Для этого командлета необходимо проверить и принять нашу лицензию и заявление о конфиденциальности.
> - Модуль инструментирования добавляет дополнительные издержки и по умолчанию отключен.

## <a name="examples"></a>Примеры

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Параметры

### <a name="-acceptlicense"></a>-AcceptLicense
**Необязательный параметр.** Используйте этот параметр, чтобы принять условия лицензии и конфиденциальности в установках без монитора.

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот параметр для вывода подробных журналов.

## <a name="output"></a>Вывод


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Пример выходных данных для успешного включения модуля инструментирования

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Следующие шаги

  Просмотр телеметрии:
 - [Изучите метрики](../../azure-monitor/app/metrics-explorer.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- Используйте [аналитику](../../azure-monitor/app/analytics.md) для более сложных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).
 
 Добавление данных телеметрии:
 - [Создайте веб-тесты](monitor-web-app-availability.md) , чтобы убедиться, что ваш сайт остается активным.
- [Добавьте данные телеметрии веб-клиента](../../azure-monitor/app/javascript.md) , чтобы просмотреть исключения из кода веб-страницы и включить вызовы трассировки.
- [Добавьте в код пакет SDK для Application Insights](../../azure-monitor/app/asp-net.md) , чтобы можно было вставить вызовы трассировки и журнала.
 
 Дополнительные возможности монитор состояния версии 2:
 - Воспользуйтесь нашим руководством по [устранению неполадок](status-monitor-v2-troubleshoot.md) монитор состояния v2.
 - [Получите конфигурацию](status-monitor-v2-api-get-config.md) , чтобы убедиться, что параметры записаны правильно.
 - [Получение состояния](status-monitor-v2-api-get-status.md) для проверки мониторинга.
