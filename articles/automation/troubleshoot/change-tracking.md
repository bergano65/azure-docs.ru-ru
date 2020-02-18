---
title: Устранение неполадок в службе Отслеживания изменений Azure
description: Узнайте, как устранить неполадки с помощью функции Отслеживание изменений и инвентаризации службы автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198536"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Устранение неполадок в службах "Отслеживание изменений" и "Учет"

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Сценарий: записи Отслеживание изменений не отображаются для компьютеров под Windows

#### <a name="issue"></a>Проблема

Вы не видите никаких Отслеживание изменений или результатов инвентаризации для компьютеров Windows, подключенных к Отслеживание изменений.

#### <a name="cause"></a>Причина

Эта ошибка может быть вызвана следующими причинами.

* Microsoft Monitoring Agent не работает.
* Подключение к учетной записи службы автоматизации блокируется.
* Пакеты управления для Отслеживание изменений не скачаны.
* Подключаемая виртуальная машина может быть взята с клонированного компьютера, который не был Sysprep с установленным Microsoft Monitoring Agent.

#### <a name="resolution"></a>Решение

Описанные ниже решения могут помочь решить проблему. Если вам по-прежнему нужна помощь, можно получить сведения о диагностике и обратиться в службу поддержки. На компьютере агента перейдите к папке C:\Program Files\Microsoft Monitoring Ажент\ажент\тулс и выполните следующие команды:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> По умолчанию трассировка ошибок включена. Чтобы включить подробные сообщения об ошибках, как в предыдущем примере, используйте параметр *ver* . Для получения информационных трассировок используйте *INF-файл* при вызове **старттраЦинг. cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent не работает

Убедитесь, что на компьютере выполняется Microsoft Monitoring Agent (HealthService. exe).

##### <a name="communication-to-automation-account-blocked"></a>Обмен данными с учетной записью службы автоматизации заблокирован

Проверьте Просмотр событий на компьютере и найдите в них все события, в которых есть слово "отслеживания изменений".

Сведения об адресах и портах, которые должны быть разрешены для работы Отслеживание изменений, см. в статье [Автоматизация ресурсов в центре обработки данных или в облаке с помощью гибридной рабочей роли Runbook](../automation-hybrid-runbook-worker.md#network-planning) .

##### <a name="management-packs-not-downloaded"></a>Пакеты управления не скачаны

Убедитесь, что следующие пакеты управления Отслеживание изменений и Inventory установлены локально:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>ВИРТУАЛЬная машина на клонированном компьютере, не Sysprep

При использовании клонированного образа сначала выполните Sysprep, а затем установите Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Сценарий: нет результатов Отслеживание изменений или инвентаризации на компьютерах Linux

#### <a name="issue"></a>Проблема

Вы не видите никаких результатов инвентаризации или Отслеживание изменений для компьютеров Linux, подключенных к Отслеживание изменений. 

#### <a name="cause"></a>Причина
Ниже приведены возможные причины, связанные с этой проблемой.
* Агент Log Analytics для Linux не работает.
* Агент Log Analytics для Linux настроен неправильно.
* Существуют конфликты мониторинга целостности файлов (FIM).

#### <a name="resolution"></a>Решение 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Агент Log Analytics для Linux не работает

Убедитесь, что управляющая программа для агента Log Analytics для Linux (omsagent) запущена на компьютере. Выполните следующий запрос в рабочей области Log Analytics, связанной с учетной записью службы автоматизации.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Если компьютер не отображается в результатах запроса, он не был недавно возвращен. Возможно, существует ошибка локальной конфигурации, и необходимо переустановить агент. Сведения об установке и настройке см. [в разделе "получение данных журнала с помощью агента log Analytics"](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Если компьютер отображается в результатах запроса, проверьте конфигурацию области. См. раздел [нацеленность на решения мониторинга в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Дополнительные сведения об устранении этой проблемы см. в разделе [проблема: данные Linux не отображаются](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Агент Log Analytics для Linux настроен неправильно

Возможно, агент Log Analytics для Linux неправильно настроен для сбора выходных данных журнала и командной строки с помощью средства сбора журналов OMS. См. раздел [мониторинг изменений в среде с помощью решения отслеживание изменений](../change-tracking.md).

##### <a name="fim-conflicts"></a>Конфликты FIM

Возможно, функция FIM центра безопасности Azure неправильно проверяет целостность файлов Linux. Убедитесь, что FIM работает и правильно настроен для мониторинга файлов Linux. См. раздел [мониторинг изменений в среде с помощью решения отслеживание изменений](../change-tracking.md).

## <a name="next-steps"></a>Следующие шаги

Если вы не видите проблему или не можете решить проблему, используйте один из следующих каналов для получения дополнительной поддержки.

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
