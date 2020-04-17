---
title: Ссылка на API агентства API для анализа приложений приложений Azure
description: Ссылка на API агента приложений. Включить-ИнструментацияEngine. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 70102395b94c89d4ce0edfddeda1df0be0e2f216
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536766"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Приложение Insights Agent API: Enable-InstrumentationEngine

В этой статье описывается cmdlet, который является членом [модуля Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Включает приборовоз, установив некоторые ключи реестра.
Перезапустите IIS для вхотворемых изменений.

Аппаратный механизм может дополнять данные, собранные SDK .NET.
Он собирает события и сообщения, описывающие выполнение управляемого процесса. Эти события и сообщения включают коды результатов зависимости, глаголы HTTP и [текст команды S'L.](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)

Включите приборный двигатель, если:
- Вы уже включили мониторинг с помощью Смдлета, но не включили приборный двигатель.
- Вы вручную сотворили свое приложение с помощью SDK .NET и хотите получить дополнительную телеметрию.

> [!IMPORTANT] 
> Для этого cmdlet требуется сеанс PowerShell с разрешениями Admin.

> [!NOTE] 
> - Это cmdlet требует, чтобы вы рассмотрели и приняли наши лицензии и конфиденциальности заявление.
> - Двигатель приборов добавляет дополнительные накладные расходы и выключен по умолчанию.

## <a name="examples"></a>Примеры

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Параметры

### <a name="-acceptlicense"></a>-ПринятьЛицензия
**Дополнительные.** Используйте этот переключатель, чтобы принять заявление о лицензии и конфиденциальности в безголовых установках.

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Используйте этот переключатель для вывода подробные журналы.

## <a name="output"></a>Выходные данные


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Пример выхода из успешного включения приборного двигателя

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Следующие шаги

  Просмотр телеметрии:
 - [Изучите метрики](../../azure-monitor/platform/metrics-charts.md) для мониторинга производительности и использования.
- [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
- Используйте [аналитику](../../azure-monitor/app/analytics.md) для более продвинутых запросов.
- [Создание панелей мониторинга.](../../azure-monitor/app/overview-dashboard.md)
 
 Добавление данных телеметрии:
 - [Создайте веб-тесты](monitor-web-app-availability.md), чтобы убедиться, что ваш сайт продолжает работать.
- [Добавьте телеметрию веб-клиента,](../../azure-monitor/app/javascript.md) чтобы увидеть исключения из кода веб-страницы и включить отслеживание вызовов.
- [Добавьте SDK Application Insights в свой код,](../../azure-monitor/app/asp-net.md) чтобы вы могли вставлять вызовы трассировки и регистрации.
 
 Сделайте больше с агентом По анализу приложений:
 - Используйте наше руководство для [устранения неполадок](status-monitor-v2-troubleshoot.md) ВИС Агента.
 - [Получите конфигурацию,](status-monitor-v2-api-get-config.md) чтобы подтвердить, что ваши настройки были записаны правильно.
 - [Получите статус](status-monitor-v2-api-get-status.md) для проверки мониторинга.
