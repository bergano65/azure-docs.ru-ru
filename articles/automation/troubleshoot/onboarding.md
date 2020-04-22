---
title: Устранение проблем с решениями по управлению Azure Automation
description: Узнайте, как устранить неполадки решения ошибок на борту.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679233"
---
# <a name="troubleshoot-solution-onboarding"></a>Решение для устранения неполадок на борту

Ошибки могут быть допущены при приеме на борт решения Update Management или решения отслеживания и инвентаризации изменений. В этой статье описаны различные ошибки, которые могут возникнуть, и способы их устранения.

## <a name="known-issues"></a>Известные проблемы

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Сценарий: Переименование зарегистрированного узла требует нерегистрации или регистрации снова

#### <a name="issue"></a>Проблемы

Узел регистрируется в Azure Automation, а затем меняется название компьютера операционной системы. Отчеты из узла продолжают отображаться с исходным именем.

#### <a name="cause"></a>Причина:

Переименование зарегистрированных узлов не обновляет имя узла в Azure Automation.

#### <a name="resolution"></a>Решение

Отменить регистрацию узла из состояния конфигурации Azure Automation, а затем зарегистрировать его снова. Отчеты, опубликованные службе до этого времени, больше не будут доступны.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Сценарий: Переподпись сертификатов через прокси https не поддерживается

#### <a name="issue"></a>Проблемы

При подключении через прокси-решение, которое завершает трафик HTTPS, а затем повторно шифрует трафик с помощью нового сертификата, служба не позволяет подключиться.

#### <a name="cause"></a>Причина:

Azure Automation не поддерживает переподписи сертификатов, используемых для шифрования трафика.

#### <a name="resolution"></a>Решение

В настоящее время нет обходного пути для этой проблемы.

## <a name="general-errors"></a>Общие ошибки

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Сценарий: Посадка не справляется с сообщением - решение не может быть включено

#### <a name="issue"></a>Проблемы

Вы получаете одно из следующих сообщений при попытке на борт VM к решению:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Причина:

Эта ошибка вызвана неправильными или отсутствующими разрешениями на VM или рабочем пространстве, или для пользователя.

#### <a name="resolution"></a>Решение

Убедитесь, что у вас есть [правильные разрешения, необходимые для бортовых машин,](../automation-role-based-access-control.md#onboarding-permissions) а затем попытаться на борту решения снова. Если вы получили `The solution cannot be enabled on this VM because the permission to read the workspace is missing`ошибку, убедитесь, что у вас есть `Microsoft.OperationalInsights/workspaces/read` разрешение, чтобы иметь возможность найти, если VM находится на борту в рабочее пространство.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Сценарий: Посадка не справляется с сообщением: Не удалось настроить учетную запись автоматизации для диагностических журналов

#### <a name="issue"></a>Проблемы

Вы получаете следующее сообщение при попытке на борт VM к решению:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Причина:

Эта ошибка может быть вызвана, если уровень ценообразования не соответствует модели выставления счетов подписки. Смотрите [использование мониторинга и сметные затраты в Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Решение

Создайте рабочее пространство Log Analytics вручную и повторите процесс посадки, чтобы выбрать созданное рабочее пространство.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Сценарий: ComputerGroupQueryFormatError

#### <a name="issue"></a>Проблемы

Этот код ошибки означает, что сохраненный запрос группы поиска, используемый для таргетинга на решение, не отформатирован правильно. 

#### <a name="cause"></a>Причина:

Возможно, вы изменили запрос, или система могла изменить его.

#### <a name="resolution"></a>Решение

Вы можете удалить запрос для решения, а затем снова на борту решения, которое воссоздает запрос. Запрос можно найти в рабочем пространстве под **сохраненным поиском.** Название запроса — **MicrosoftDefaultComputerGroup,** а категория запроса — название связанного с ним решения. Если включено несколько решений, запрос **MicrosoftDefaultComputerGroup** показывает несколько раз в **сохраненных поисках.**

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Сценарий: PolicyViolation

#### <a name="issue"></a>Проблемы

Этот код ошибки указывает на то, что развертывание не удалось из-за нарушения одной или нескольких политик.

#### <a name="cause"></a>Причина: 

Политика блокирует выполнение операции.

#### <a name="resolution"></a>Решение

Для успешного развертывания решения необходимо рассмотреть вопрос об изменении указанной политики. Поскольку существует множество различных типов политик, которые могут быть определены, требуемые изменения зависят от нарушаемой политики. Например, если политика определена в группе ресурсов, которая отказывает в разрешении на изменение содержимого некоторых содержащихся ресурсов, можно выбрать одно из этих исправлений:

* Полностью удалите политику.
* Попробуйте на борт решения для другой группы ресурсов.
* Переориентируйте политику на определенный ресурс, например учетную запись Автоматизации.
* Пересмотрите набор ресурсов, которые политика настроена для отказа.

Проверьте уведомления в правом верхнем углу портала Azure или перейдите к группе ресурсов, содержащей учетную запись Automation, и выберите **развертывания** в **настройках** для просмотра неудавшегося развертывания. Подробнее о политике Azure читайте [в обзоре политики Azure.](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Сценарий: Ошибки, пытающиеся отсоединить рабочее пространство

#### <a name="issue"></a>Проблемы

Вы получаете следующую ошибку при попытке отсоединить рабочее пространство:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает, когда в рабочем пространстве Log Analytics по-прежнему есть решения, которые зависят от вашего рабочего пространства счета автоматизации и анализа журналов.

### <a name="resolution"></a>Решение

Удалите следующие решения из рабочего пространства, если вы используете их:

* Управление обновлениями
* Отслеживание изменений и инвентаризация
* Запуск и остановка виртуальных машин в нерабочее время

После удаления решений можно отменить рабочее пространство. Важно очистить все существующие артефакты из этих решений из рабочего пространства и учетной записи Автоматизации 

* Для управления обновлением удалите развертывание обновлений (Расписание) из учетной записи автоматизации.
* Для запуска/остановки ВМ в нерабочее время удалите все блокировки компонентов решения в учетной **записи автоматизации** > под**настройками.** [См. Удалить Start/Stop VMs в нерабочее время решения.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Аналитика журналов для сбоев расширения Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Установка агента Log Analytics для расширения Windows может выполнить неудачу по ряду причин. В следующем разделе описаны проблемы с бортовой работой, которые могут привести к сбоям при развертывании агента Log Analytics для расширения Windows.

>[!NOTE]
>Агент журналной аналитики для Windows — это имя, используемое в настоящее время в Azure Automation для агента мониторинга Майкрософт (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Сценарий: Исключение во время запроса WebClient

Аналитика журнала для расширения Windows на VM не может общаться с внешними ресурсами, и развертывание завершается неудачей.

#### <a name="issue"></a>Проблемы

Ниже приведены примеры возвращаемых сообщений об ошибках.

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Причина:

Возможные причины возникновения этой ошибки перечислены ниже.

* Прокси, настроенный в VM, позволяет только определенные порты.
* Настройки брандмауэра, которые блокируют доступ к запрашиваемым адресам и портам.

#### <a name="resolution"></a>Решение

Убедитесь в том, что у вас открыты порты и адреса, которые необходимы для сеанса связи. Список портов и адресов можно узнать в разделе [Настройка сети](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Сценарий: Установка не удалось из-за проблем с переходной средой

Установка Log Analytics для расширения Windows не удалась во время развертывания из-за другой установки или действия, блокирующей установку

#### <a name="issue"></a>Проблемы

Ниже приведены примеры сообщений об ошибках, которые могут отображаться на экране.

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Причина:

Возможные причины возникновения этой ошибки перечислены ниже.

* Еще одна установка находится в процессе.
* Система срабатывает для перезагрузки во время развертывания шаблона.

#### <a name="resolution"></a>Решение

Эта ошибка является переходной по своему характеру. Чтобы установить расширение, необходимо повторить развертывание.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Сценарий: время ожидания установки истекло

Установка Log Analytics для расширения Windows не завершена из-за тайм-аута.

#### <a name="issue"></a>Проблемы

Ниже приводится пример сообщения об ошибке, которое может быть возвращено:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Причина:

Этот тип ошибки происходит потому, что VM находится под большой нагрузкой во время установки.

### <a name="resolution"></a>Решение

Попробуйте установить агент Log Analytics для расширения Windows, когда VM находится под более низкой нагрузкой.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите проблему выше или не можете решить вашу проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
