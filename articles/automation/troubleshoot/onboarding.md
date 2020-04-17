---
title: Устранение проблем в области управления Azure Automation
description: Сведения о том, как устранить ошибки подключения с помощью решений управления обновлениями, отслеживания изменений и инвентаризации
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536970"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Ошибки при посадке на управление обновлением, отслеживание изменений и инвентаризации

Ошибки могут возникнуть при подключении таких решений, как отслеживание изменений или управление обновлениями и инвентаризация. В этой статье описываются различные ошибки, которые могут возникнуть, и пути их решения.

## <a name="known-issues"></a>Известные проблемы

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Сценарий: Переименование зарегистрированного узла требует нерегистрации / регистрации снова

#### <a name="issue"></a>Проблемы

Узел регистрируется в Azure Automation, а затем имя компьютера операционной системы меняется.  Отчеты из узла продолжают отображаться с исходным именем.

#### <a name="cause"></a>Причина:

Переименование зарегистрированных узлов не обновляет имя узла в Azure Automation.

#### <a name="resolution"></a>Решение

Отменить регистрацию узла из состояния конфигурации Azure Automation, а затем зарегистрировать его снова.  Отчеты, опубликованные службе до этого времени, больше не будут доступны.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Сценарий: Переподпись сертификатов через прокси https не поддерживается

#### <a name="issue"></a>Проблемы

Клиенты сообщили, что при подключении через прокси-решение, которое завершает трафик https, а затем повторно шифрует трафик с помощью нового сертификата, служба не позволяет подключение.

#### <a name="cause"></a>Причина:

Azure Automation не поддерживает переподписи сертификатов, используемых для шифрования трафика.

#### <a name="resolution"></a>Решение

Решение для этой проблемы отсутствует.

## <a name="general-errors"></a>Общие ошибки

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Сценарий: Посадка не справляется с сообщением - решение не может быть включено

#### <a name="issue"></a>Проблемы

Вы получаете одно из следующих сообщений при попытке на борт виртуальной машины к решению:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Причина:

Эта ошибка вызвана неправильными или отсутствующими разрешениями на виртуальной машине, рабочем пространстве или для пользователя.

#### <a name="resolution"></a>Решение

Убедитесь, что у вас есть разрешение на подключение виртуальной машины. Просмотрите [разрешения, необходимые для подключения компьютеров](../automation-role-based-access-control.md#onboarding-permissions), и попробуйте еще раз подключить решение. Если вы получили `The solution cannot be enabled on this VM because the permission to read the workspace is missing`ошибку, убедитесь, что у вас есть `Microsoft.OperationalInsights/workspaces/read` разрешение, чтобы иметь возможность найти, если VM находится на борту в рабочее пространство.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Сценарий: Посадка не справляется с сообщением - Не удалось настроить учетную запись автоматизации для диагностической регистрации

#### <a name="issue"></a>Проблемы

При попытке подключить виртуальную машину к решению вы получаете следующее сообщение:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Причина:

Эта ошибка может быть вызвана, если уровень ценообразования не соответствует модели выставления счетов подписки. Для получения дополнительной информации [см.](https://aka.ms/PricingTierWarning)

#### <a name="resolution"></a>Решение

Создайте рабочее пространство Log Analytics вручную и повторите процесс посадки, чтобы выбрать созданное рабочее пространство.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Сценарий: ComputerGroupQueryFormatError

#### <a name="issue"></a>Проблемы

Этот код ошибки означает, что сохраненный запрос на поиск группы компьютеров, который используется в этом решении, имеет неправильный формат. 

#### <a name="cause"></a>Причина:

Возможно, вы неудачно изменили запрос или автоматическое изменение внесено системой.

#### <a name="resolution"></a>Решение

Вы можете удалить запрос для этого решения и заново подключить решение. Запрос также будет создан заново. Текущий запрос можно найти в разделе **Сохраненные поисковые запросы** рабочей области. Этот запрос имеет имя **MicrosoftDefaultComputerGroup** и категорию, имя которой совпадает с именем связанного решения. Если включены сразу несколько решений, группа **MicrosoftDefaultComputerGroup** отображается в области **Сохраненные условия поиска** несколько раз.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Сценарий: PolicyViolation

#### <a name="issue"></a>Проблемы

Этот код ошибки означает, что развертывание завершилось неудачей из-за нарушения одной или нескольких политик.

#### <a name="cause"></a>Причина: 

Политика находится в таком месте, из которого выполнение операции невозможно.

#### <a name="resolution"></a>Решение

Чтобы успешно развернуть решение, необходимо учитывать изменение указанной политики. Поскольку можно определить много разных политик, необходимые изменения зависят от нарушенной политики. Например, если политика определена в группе ресурсов, которая отказала в разрешении на изменение содержимого определенных типов ресурсов, можно выполнить одно из следующих действий.

* Полностью удалите политику.
* Попробуйте подключиться к другой группе ресурсов.
* Пересмотрите политику:
  * повторно примените политику к конкретному ресурсу (например, к конкретной учетной записи службы автоматизации);
  * пересмотрите набор ресурсов, к которым политика запрещает доступ.

Проверьте уведомления в правом верхнем углу портала Azure или перейдите к группе ресурсов, содержащей учетную запись автоматизации, и выберите **развертывания** в **настройках** для просмотра неудавшегося развертывания. Дополнительные сведения о политике Azure см. в [обзоре политики Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Сценарий: Ошибки, пытающиеся отсоединить рабочее пространство

#### <a name="issue"></a>Проблемы

Вы получаете следующую ошибку при попытке отсоединить рабочее пространство:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает, когда в рабочем пространстве Log Analytics по-прежнему имеются решения, зависят от связи рабочего пространства с учетной записью автоматизации и анализа журналов.

### <a name="resolution"></a>Решение

Для решения этой проблемы необходимо удалить следующие решения из рабочего пространства, если вы используете их:

* Управление обновлениями
* Отслеживание изменений
* Запуск и остановка виртуальных машин в нерабочее время

Как только вы удалите решения, вы можете отсоединить рабочее пространство. Важно также очистить все существующие артефакты из этих решений из рабочего пространства и учетной записи автоматизации.  

* Управление обновлениями
  * Удалите развертывание обновлений (Расписание) из учетной записи автоматизации
* Запуск и остановка виртуальных машин в нерабочее время
  * Удалите все блокировки компонентов решения в учетной **записи автоматизации** > под**настройками.**
  * Для дополнительных шагов, чтобы удалить Start/Stop VMs [Remove the Start/Stop VM during off-hours solution](../automation-solution-vm-management.md#remove-the-solution)в нерабочее время решение см.

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>Устранение неполадок расширения MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

При развертывании решения, также развертывается множество связанных ресурсов. Одним из этих ресурсов является расширение Microsoft Monitoring Agent или агент Log Analytics для Linux. Это виртуальные расширения машины, установленные гостевом агентом виртуальной машины, который отвечает за связь с настроенным рабочим пространством Log Analytics, с целью последующей координации загрузки бинарных файлов и других файлов, от чего зависит решение, от которого вы будете зависеть после его начала выполнения.
Обычно об ошибках установки MMA или агента Log Analytics для Linux можно узнать из уведомления, которое появляется в центре уведомлений. Щелкнув уведомление, можно получить дополнительные сведения о конкретной ошибке. Дополнительные сведения о произошедших сбоях развертывания можно получить, перейдя к элементу "Развертывания" в разделе "Группы ресурсов".
К неудачной установке MMA или агента Log Analytics для Linux могут привести множество причин, а шаги, которые необходимо предпринять для их устранения, в каждом случае отличаются. Действия по устранению неполадок подробнее описываются далее.

В следующем разделе описаны различные проблемы, с которыми можно встретить при посадке, которые вызывают сбой в развертывании расширения MMA.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Сценарий: Исключение во время запроса WebClient

Расширению MMA виртуальной машины не удается установить связь с внешними ресурсами, что приводит к ошибке развертывания.

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

* В VM настроен прокси- и позволяет только определенные порты.

* Настройки брандмауэра, которые блокируют доступ к запрашиваемым адресам и портам.

#### <a name="resolution"></a>Решение

Убедитесь в том, что у вас открыты порты и адреса, которые необходимы для сеанса связи. Список портов и адресов можно узнать в разделе [Настройка сети](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Сценарий: Установка не удалось из-за проблем с переходной средой

Установка расширения агента мониторинга Майкрософт не удалась во время развертывания из-за другой установки или действия, блокирующей установку

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

* Запущен другой процесс установки
* Система срабатывает для перезагрузки во время развертывания шаблона

#### <a name="resolution"></a>Решение

По своей природе эта ошибка является временной. Чтобы установить расширение, необходимо повторить развертывание.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Сценарий: время ожидания установки истекло

Установка расширения ММА не завершена из-за тайм-аута.

#### <a name="issue"></a>Проблемы

Следующим примером является сообщение об ошибке, которое может быть возвращено:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает из-за того, что виртуальная машина находилась под большой нагрузкой во время установки.

### <a name="resolution"></a>Решение

Попытайтесь установить расширение MMA при низкой загруженности виртуальной машины.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Связь [@AzureSupport](https://twitter.com/azuresupport) с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
