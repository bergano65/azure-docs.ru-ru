---
title: Сбор событий трассировки событий Windows (ETW) для анализа Azure Monitor журналов
description: Узнайте, как осуществлять трассировку событий Windows (ETW) для анализа в журналах Azure Monitor.
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 6239cf48794c74c5dd810fda42476df399300578
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223801"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Сбор событий трассировки событий Windows (ETW) для анализа Azure Monitor журналов

Средство *трассировки событий для Windows (ETW)* предоставляет механизм для инструментирования приложений пользовательского режима и драйверов режима ядра. Агент Log Analytics используется для получения [событий Windows](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events) , записываемых в административные и операционные [каналы ETW](https://docs.microsoft.com/windows/win32/wes/eventmanifestschema-channeltype-complextype). Однако иногда требуется записывать и анализировать другие события, например, записанные в канал аналитики.  

## <a name="event-flow"></a>Поток событий

Чтобы успешно собираются [события ETW на основе манифеста](https://docs.microsoft.com/windows/win32/etw/about-event-tracing#types-of-providers) для анализа в журналах Azure Monitor, необходимо использовать [расширение системы диагностики Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview) для Windows (WAD). В этом сценарии расширение диагностики выступает в качестве объекта-получателя ETW, записывающего события в службу хранилища Azure (таблицы) в качестве промежуточного хранилища. Здесь он будет храниться в таблице с именем **WADETWEventTable**. Log Analytics собирает данные таблицы из службы хранилища Azure, предоставляя их в виде таблицы с именем **етвевент**.

![Поток событий](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>Настройка сбора журналов ETW

### <a name="step-1-locate-the-correct-etw-provider"></a>Шаг 1. Определение правильного поставщика ETW

Используйте одну из следующих команд для перечисления поставщиков ETW в исходной системе Windows.

Командная строка:

```
logman query providers
```

PowerShell.
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
При необходимости вы можете передать эти выходные данные PowerShell в Out-Gridview для удобства навигации.

Запишите имя поставщика ETW и идентификатор GUID, который соответствует аналитическому или отладочному журналу, представленному в Просмотр событий, или модулю, для которого требуется получить данные событий.

### <a name="step-2-diagnostics-extension"></a>Шаг 2. расширение системы диагностики

Убедитесь, что *расширение системы диагностики Windows* [установлено](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-windows-install#install-with-azure-portal) во всех исходных системах.

### <a name="step-3-configure-etw-log-collection"></a>Шаг 3. Настройка сбора журналов ETW

1. Перейдите в колонку " **параметры диагностики** " виртуальной машины.

2. Выбор вкладки " **журналы** "

3. Прокрутите вниз и включите параметр " **события трассировки событий Windows" (ETW)** на ![ снимке экрана параметров диагностики.](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Задайте идентификатор GUID поставщика или класса поставщика на основе поставщика, для которого настраивается коллекция.

5. Задайте соответствующий [**уровень ведения журнала**](https://docs.microsoft.com/windows/win32/etw/configuring-and-starting-an-event-tracing-session) .

6. Щелкните многоточие рядом с предоставляемым поставщиком и нажмите кнопку **настроить** .

7. Убедитесь, что для **целевой таблицы по умолчанию** задано значение **етвевенттабле** .

8. Задать [**Фильтр ключевых слов**](https://docs.microsoft.com/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) , если это необходимо

9. Сохранение параметров поставщика и журнала

После создания соответствующих событий следует начать просмотр событий ETW, появляющихся в таблице **WADetweventtable** в службе хранилища Azure. Для подтверждения можно использовать Обозреватель службы хранилища Azure.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Шаг 4. Настройка сбора учетных записей хранения Log Analytics

Выполните [эти инструкции](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) , чтобы получить журналы из службы хранилища Azure. После настройки данные событий ETW должны появиться в Log Analytics в таблице **етвевент** .

## <a name="next-steps"></a>Дальнейшие действия
- Использование [настраиваемых полей](https://docs.microsoft.com/azure/azure-monitor/platform/custom-fields) для создания структуры в событиях ETW
- Узнайте больше о [запросах журнала](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview), которые можно применять для анализа данных, собираемых из источников данных и решений.
