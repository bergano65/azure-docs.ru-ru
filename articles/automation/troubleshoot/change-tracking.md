---
title: Устранение проблем с Отслеживание изменений и инвентаризацией
description: Узнайте, как устранять неполадки и решать проблемы с помощью решения службы автоматизации Azure Отслеживание изменений и инвентаризации.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582145"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Устранение неполадок Отслеживание изменений и инвентаризации

В этой статье описывается, как устранять неполадки Отслеживание изменений и инвентаризации службы автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации модули можно обновить до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Сценарий: Отслеживание изменений и записи инвентаризации не отображаются для компьютеров под Windows

#### <a name="issue"></a>Проблема

Вы не видите никаких Отслеживание изменений и результатов инвентаризации для подключенных компьютеров Windows.

#### <a name="cause"></a>Причина

Эта ошибка может быть вызвана следующими причинами.

* Агент Azure Log Analytics для Windows не работает.
* Подключение к учетной записи службы автоматизации блокируется.
* Пакеты управления Отслеживание изменений и Inventory не скачаны.
* Подключаемая виртуальная машина может быть взята с клонированного компьютера, который не Sysprep с установленным агентом Log Analytics для Windows.

#### <a name="resolution"></a>Решение

На компьютере агента Log Analytics перейдите в каталог **C:\Program Files\Microsoft Monitoring ажент\ажент\тулс** и выполните следующие команды:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Если вам по-прежнему нужна помощь, можно получить сведения о диагностике и обратиться в службу поддержки.

> [!NOTE]
> По умолчанию агент Log Analytics включает трассировку ошибок. Чтобы включить подробные сообщения об ошибках, как в предыдущем примере, используйте `VER` параметр. Для информационных трассировок используйте `INF` при вызове. `StartTracing.cmd`

##### <a name="log-analytics-agent-for-windows-not-running"></a>Агент Log Analytics для Windows не работает

Убедитесь, что на компьютере выполняется агент Log Analytics для Windows (**HealthService. exe**).

##### <a name="communication-to-automation-account-blocked"></a>Обмен данными с учетной записью службы автоматизации заблокирован

Проверьте Просмотр событий на компьютере и найдите все события, в которых есть слово `changetracking` .

Сведения об адресах и портах, которые необходимо разрешить для работы Отслеживание изменений и инвентаризации, см. в статье [Автоматизация ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Пакеты управления не скачаны

Убедитесь, что следующие пакеты управления Отслеживание изменений и Inventory установлены локально:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>ВИРТУАЛЬная машина на клонированном компьютере, не Sysprep

При использовании клонированного образа сначала выполните Sysprep, а затем установите агент Log Analytics для Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Сценарий: нет результатов Отслеживание изменений и инвентаризации на компьютерах Linux

#### <a name="issue"></a>Проблема

Вы не видите никаких Отслеживание изменений и результатов инвентаризации для компьютеров Linux, подключенных к решению. 

#### <a name="cause"></a>Причина
Ниже приведены возможные причины, связанные с этой проблемой.
* Агент Log Analytics для Linux не работает.
* Агент Log Analytics для Linux настроен неправильно.
* Существуют конфликты мониторинга целостности файлов (FIM).

#### <a name="resolution"></a>Решение 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Агент Log Analytics для Linux не работает

Убедитесь, что управляющая программа для агента Log Analytics для Linux (**omsagent**) запущена на компьютере. Выполните следующий запрос в рабочей области Log Analytics, связанной с учетной записью службы автоматизации.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Если компьютер не отображается в результатах запроса, он не был недавно возвращен. Возможно, существует ошибка локальной конфигурации, и необходимо переустановить агент. Сведения об установке и настройке см. [в разделе "получение данных журнала с помощью агента log Analytics"](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Если компьютер отображается в результатах запроса, проверьте конфигурацию области. См. раздел [нацеленность на решения мониторинга в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Дополнительные сведения об устранении этой проблемы см. в разделе [проблема. данные Linux не отображаются](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Агент Log Analytics для Linux настроен неправильно

Возможно, агент Log Analytics для Linux неправильно настроен для сбора выходных данных журнала и командной строки с помощью средства сбора журналов OMS. [В статье мониторинг изменений в среде с помощью решения отслеживание изменений и инвентаризации](../change-tracking.md).

##### <a name="fim-conflicts"></a>Конфликты FIM

Возможно, функция FIM центра безопасности Azure неправильно проверяет целостность файлов Linux. Убедитесь, что FIM работает и правильно настроен для мониторинга файлов Linux. [В статье мониторинг изменений в среде с помощью решения отслеживание изменений и инвентаризации](../change-tracking.md).

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите проблему здесь или вы не можете решить проблему, попробуйте использовать один из следующих каналов для получения дополнительной поддержки:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с помощью официальной учетной записи Microsoft Azure для улучшения качества взаимодействия с [@AzureSupport](https://twitter.com/azuresupport)клиентами. Служба поддержки Azure подключается к сообществу Azure для получения ответов, поддержки и экспертов.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/)и выберите **получить поддержку**.
