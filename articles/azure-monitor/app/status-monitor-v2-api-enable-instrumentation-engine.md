---
title: 'Монитор состояния v2 Справочник по API Azure: Включить инструментирование ядро | Документация Майкрософт'
description: Справочник по API v2 монитор состояния. Enable-InstrumentationEngine. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложений ASP.NET, размещенным на предприятиях, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514372"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>Состояние монитора v2 API: Enable-InstrumentationEngine (v0.2.1-альфа-версия)

В этой статье описывается командлет, который является членом [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Описание

Включает ядро инструментирования, устанавливая некоторые разделы реестра.
Перезапустите службы IIS, чтобы изменения вступили в силу.

Ядро инструментирования можно дополнить данные, собранные пакеты SDK для .NET.
Он собирает события и сообщения, которые описывают выполнение управляемого процесса. Эти события и сообщения включают коды результатов зависимостей, HTTP-команды и текст команды SQL.

Включите модуль инструментирования, если:
- Вы уже включили мониторинг с помощью командлета Enable, но не включали ядро инструментирования.
- Вручную инструментирование приложения с помощью пакетов SDK для .NET и чтобы собрать дополнительные данные телеметрии.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

> [!NOTE] 
> - Для этого командлета требуется, просмотрите и примите наши лицензии и заявления о конфиденциальности.
> - Модуль инструментирования требует дополнительных затрат и отключен по умолчанию.

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
 - [Изучение метрик](../../azure-monitor/app/metrics-explorer.md) для контроля производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- Используйте [analytics](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
- [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).
 
 Добавление данных телеметрии:
 - [Создание веб-тестов](monitor-web-app-availability.md) чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента](../../azure-monitor/app/javascript.md) чтобы просматривать исключения в коде веб-страницы и включить вызовы трассировки.
- [Добавьте пакет SDK Application Insights в код](../../azure-monitor/app/asp-net.md) чтобы можно было вставить трассировки и журналов вызовов.
 
 Новые возможности в версии 2 монитор состояния:
 - Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
 - [Получить конфигурацию](status-monitor-v2-api-get-config.md) для подтверждения того, что параметры были записаны неправильно.
 - [Получить состояние](status-monitor-v2-api-get-status.md) для проверки наблюдения.
