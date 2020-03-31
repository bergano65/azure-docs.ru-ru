---
title: Устранение неполадок в службе Отслеживания изменений Azure
description: Узнайте, как устранить неполадки и устранить проблемы с функцией отслеживания и инвентаризации изменений автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198536"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Устранение неполадок в службах "Отслеживание изменений" и "Учет"

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Сценарий: Записи отслеживания изменений не отображаются для компьютеров Windows

#### <a name="issue"></a>Проблема

Вы не видите результатов отслеживания изменений или инвентаризации для компьютеров Windows, которые находятся на борту для отслеживания изменений.

#### <a name="cause"></a>Причина

Эта ошибка может иметь следующие причины:

* Агент мониторинга Майкрософт не работает.
* Связь с учетной записью Automation блокируется.
* Пакеты управления для отслеживания изменений не загружаются.
* VM время на борту, возможно, пришли из клонированной машины, которая не была sysprepped с Microsoft мониторинга агента установлен.

#### <a name="resolution"></a>Решение

Описанные ниже решения могут помочь решить вашу проблему. Если вам все еще нужна помощь, вы можете собрать информацию о диагностике и связаться с нами. На машине агента перейдите на C: «Файлы программы» (Агент мониторинга Майкрософт) Исполнить инструменты и запустите следующие команды:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> По умолчанию включена отслеживание ошибок. Для включения многословных сообщений об ошибках, как в предыдущем примере, используйте параметр *VER.* Для получения информации, используйте *INF* при использовании **StartTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Агент мониторинга Майкрософт не работает

Убедитесь, что на компьютере работает агент по мониторингу Microsoft (HealthService.exe).

##### <a name="communication-to-automation-account-blocked"></a>Заблокирована связь с аккаунтом Автоматизации

Проверьте Viewer событий на машине и ищите любые события, в которых есть слово «изменение» в них.

Просматривайте [ресурсы автоматизации в центре обработки данных или в облаке, используя Hybrid Runbook Worker,](../automation-hybrid-runbook-worker.md#network-planning) чтобы узнать об адресах и портах, которые должны быть разрешены для отслеживания изменений для работы.

##### <a name="management-packs-not-downloaded"></a>Пакеты управления не загружены

Убедитесь, что следующие пакеты отслеживания изменений и управления запасами установлены локально:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM из клонированной машины, которая не была sysprepped

При использовании клонированного изображения сначала установите изображение, а затем установите агент мониторинга Майкрософт.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Сценарий: Нет результатов отслеживания изменений или инвентаризации на машинах Linux

#### <a name="issue"></a>Проблема

Вы не видите результатов отслеживания запасов или изменений для машин Linux, которые предназначены для отслеживания изменений. 

#### <a name="cause"></a>Причина
Вот возможные причины, специфические для этой проблемы:
* Агент Log Analytics для Linux не работает.
* Агент Log Analytics для Linux настроен неправильно.
* Существуют конфликты по мониторингу целостности файлов (FIM).

#### <a name="resolution"></a>Решение 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Агент журналной аналитики для Linux не работает

Убедитесь, что на вашей машине работает daemon для агента Log Analytics для Linux (omsagent). Выполнить следующий запрос в рабочей области Log Analytics, который связан с вашей учетной записью Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Если вы не видите свою машину в результатах запроса, она недавно зарегистрировалась. Вероятно, есть проблема локальной конфигурации, и вы должны переустановить агент. Для получения информации об установке и конфигурации [см.](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 

Если машина отображается в результатах запроса, проверьте конфигурацию области. Смотрите [решения для мониторинга таргетинга в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Для более устранения неполадок этой проблемы, обратитесь к [вопросу: Вы не видите данных Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Агент журналной аналитики для Linux не настроен правильно

Агент Log Analytics для Linux может быть неправильно настроен для сбора данных журнала и командной строки с помощью инструмента OMS Log Collector. Просматривайте [изменения в среде с помощью решения отслеживания изменений.](../change-tracking.md)

##### <a name="fim-conflicts"></a>Конфликты FIM

Функция FIM центра безопасности Azure может быть неправильной проверкой целостности файлов Linux. Убедитесь, что FIM работает и правильно настроен для мониторинга файлов Linux. Просматривайте [изменения в среде с помощью решения отслеживания изменений.](../change-tracking.md)

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите свою проблему или не в состоянии решить вашу проблему, используйте один из следующих каналов для получения дополнительной поддержки.

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связь [@AzureSupport](https://twitter.com/azuresupport) с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
