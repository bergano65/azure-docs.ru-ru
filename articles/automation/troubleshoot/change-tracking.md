---
title: Проблемы устранения неполадок с отслеживанием изменений и инвентаризацией
description: Узнайте, как устранить неполадки и устранить проблемы с решением отслеживания и инвентаризации изменений автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679354"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Проблемы отслеживания и инвентаризации неполадок

В этой статье описывается, как устранить проблемы отслеживания изменений и инвентаризации.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Сценарий: Записи отслеживания и инвентаризации изменений не отображаются для машин Windows

#### <a name="issue"></a>Проблемы

Вы не видите результатов отслеживания изменений и инвентаризации для компьютеров Windows, которые находятся на борту.

#### <a name="cause"></a>Причина:

Эта ошибка может иметь следующие причины:

* Агент Log Analytics для Windows не работает.
* Связь с учетной записью Automation блокируется.
* Пакеты отслеживания изменений и управления запасами не загружаются.
* VM время на борту, возможно, пришли из клонированной машины, которая не была sysprepped с агентом журнала Analytics для Windows установлен.

#### <a name="resolution"></a>Решение

На машине агента Log Analytics перейдите на **C: «Файлы программы»-«Агент мониторинга Майкрософт» и** запустите следующие команды:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Если вам все еще нужна помощь, вы можете собрать информацию о диагностике и связаться с нами. 

> [!NOTE]
> Агент Log Analyticss позволяет отслеживать ошибки по умолчанию. Для включения многословных сообщений об ошибках, как в предыдущем примере, `VER` используйте параметр. Для трассировки информации используйте `INF` при вызове `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Агент аналитики журнала для Windows не работает

Убедитесь, что агент Log Analytics для Windows **(HealthService.exe)** работает на машине.

##### <a name="communication-to-automation-account-blocked"></a>Заблокирована связь с аккаунтом Автоматизации

Проверьте компонент Просмотр событий на компьютере, а также найдите любые события, которые содержат слово `changetracking`.

Просматривайте [ресурсы автоматизации в центре обработки данных или в облаке, используя Hybrid Runbook Worker,](../automation-hybrid-runbook-worker.md#network-planning) чтобы узнать об адресах и портах, которые должны быть разрешены для отслеживания изменений и инвентаризации для работы.

##### <a name="management-packs-not-downloaded"></a>Пакеты управления не загружены

Убедитесь, что следующие пакеты отслеживания изменений и управления запасами установлены локально:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM из клонированной машины, которая не была sysprepped

При использовании клонированного изображения сначала установите изображение, а затем установите агент Log Analytics для Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Сценарий: Нет результатов отслеживания изменений и инвентаризации на машинах Linux

#### <a name="issue"></a>Проблемы

Вы не видите результатов отслеживания запасов и изменений для машин Linux, которые находятся на борту решения. 

#### <a name="cause"></a>Причина:
Вот возможные причины, специфические для этой проблемы:
* Агент Log Analytics для Linux не работает.
* Агент Log Analytics для Linux настроен неправильно.
* Существуют конфликты по мониторингу целостности файлов (FIM).

#### <a name="resolution"></a>Решение 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Агент журналной аналитики для Linux не работает

Убедитесь, что daemon для агента Log Analytics для Linux **(omsagent**) работает на вашей машине. Выполнить следующий запрос в рабочей области Log Analytics, который связан с вашей учетной записью Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Если вы не видите свою машину в результатах запроса, она недавно зарегистрировалась. Вероятно, есть проблема локальной конфигурации, и вы должны переустановить агент. Для получения информации об установке и конфигурации [см.](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 

Если машина отображается в результатах запроса, проверьте конфигурацию области. Смотрите [решения для мониторинга таргетинга в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Для более устранения неполадок этой проблемы [см.](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Агент журналной аналитики для Linux не настроен правильно

Агент Log Analytics для Linux может быть неправильно настроен для сбора данных журнала и командной строки с помощью инструмента OMS Log Collector. Просматривайте [изменения в среде с помощью решения отслеживания и инвентаризации изменений.](../change-tracking.md)

##### <a name="fim-conflicts"></a>Конфликты FIM

Функция FIM центра безопасности Azure может быть неправильной проверкой целостности файлов Linux. Убедитесь, что FIM работает и правильно настроен для мониторинга файлов Linux. Просматривайте [изменения в среде с помощью решения отслеживания и инвентаризации изменений.](../change-tracking.md)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите проблему выше или не можете решить вашу проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
