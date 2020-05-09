---
title: Устранение неполадок при адаптации решений управления службой автоматизации Azure
description: Узнайте, как устранять ошибки при адаптации решения службы автоматизации Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 371094ecba5168fd32a7af9fb81a71eb722efc91
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836535"
---
# <a name="troubleshoot-solution-onboarding"></a>Устранение неполадок при адаптации решения

Вы можете получить сообщения об ошибках при подключении решения Управление обновлениями службы автоматизации Azure или решения Отслеживание изменений и инвентаризации. В этой статье описаны различные ошибки, которые могут возникнуть, и способы их устранения.

## <a name="known-issues"></a>Известные проблемы

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Сценарий: для переименования зарегистрированного узла требуется отмена регистрации или повторная регистрация

#### <a name="issue"></a>Проблема

Узел зарегистрирован в службе автоматизации Azure, а затем имя компьютера операционной системы изменится. Отчеты с узла продолжают отображаться с исходным именем.

#### <a name="cause"></a>Причина

При переименовании зарегистрированных узлов имя узла в службе автоматизации Azure не обновляется.

#### <a name="resolution"></a>Решение

Отмените регистрацию узла в конфигурации состояния службы автоматизации Azure и снова зарегистрируйте его. Отчеты, опубликованные в службе до тех пор, пока это время больше не будут доступны.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Сценарий: повторное подписание сертификатов через прокси-сервер HTTPS не поддерживается

#### <a name="issue"></a>Проблема

При подключении через прокси-решение, которое завершает трафик HTTPS, а затем повторно шифрует трафик с помощью нового сертификата, служба не разрешает подключение.

#### <a name="cause"></a>Причина

Служба автоматизации Azure не поддерживает повторное подписание сертификатов, используемых для шифрования трафика.

#### <a name="resolution"></a>Решение

В настоящее время решение этой проблемы отсутствует.

## <a name="general-errors"></a>Общие ошибки

### <a name="scenario-onboarding-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Сценарий: сбой адаптации с сообщением "не удается включить решение"

#### <a name="issue"></a>Проблема

При попытке подключить виртуальную машину к решению вы получаете одно из следующих сообщений:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Причина

Эта ошибка вызвана неверными или отсутствующими разрешениями на виртуальную машину или рабочую область или для пользователя.

#### <a name="resolution"></a>Решение

Убедитесь, что у вас есть [необходимые разрешения для подключения к компьютерам](../automation-role-based-access-control.md#onboarding-permissions), и повторите попытку подключения решения. Если появится сообщение `The solution cannot be enabled on this VM because the permission to read the workspace is missing`об ошибке, убедитесь, что у вас `Microsoft.OperationalInsights/workspaces/read` есть разрешение на возможность найти виртуальную машину в рабочей области.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Сценарий: сбой адаптации с сообщением "не удалось настроить учетную запись службы автоматизации для ведения журнала диагностики"

#### <a name="issue"></a>Проблема

При попытке подключить виртуальную машину к решению появляется следующее сообщение:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Причина

Эта ошибка может возникать, если ценовая категория не соответствует модели выставления счетов подписки. Дополнительные сведения см. [в разделе Мониторинг использования и оценка затрат в Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Решение

Создайте рабочую область Log Analytics вручную и повторите процедуру адаптации, чтобы выбрать созданную рабочую область.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Сценарий: ComputerGroupQueryFormatError

#### <a name="issue"></a>Проблема

Этот код ошибки означает, что сохраненный запрос группы поисковых компьютеров, используемый для решения, имеет неправильный формат. 

#### <a name="cause"></a>Причина

Возможно, вы изменили запрос, или система могла изменить его.

#### <a name="resolution"></a>Решение

Вы можете удалить запрос для решения, а затем подключить решение еще раз, что приведет к повторному созданию запроса. Запрос можно найти в рабочей области в разделе **сохраненные условия поиска**. Имя запроса — **микрософтдефаулткомпутерграуп**, а Категория запроса — имя связанного решения. Если включено несколько решений, запрос **микрософтдефаулткомпутерграуп** отображает несколько раз при **сохраненном поиске**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Сценарий: PolicyViolation

#### <a name="issue"></a>Проблема

Этот код ошибки указывает, что развертывание завершилось сбоем из-за нарушения одной или нескольких политик.

#### <a name="cause"></a>Причина 

Политика блокирует завершение операции.

#### <a name="resolution"></a>Решение

Для успешного развертывания решения необходимо изменить указанную политику. Так как существует множество различных типов политик, которые можно определить, необходимые изменения зависят от нарушенной политики. Например, если политика определена в группе ресурсов, которая запрещает изменять содержимое некоторых автономных ресурсов, можно выбрать одно из следующих исправлений:

* Полностью удалите политику.
* Попробуйте подключить решение к другой группе ресурсов.
* Перенацеливание политики на конкретный ресурс, например в учетную запись службы автоматизации.
* Измените набор ресурсов, для которых политика блокируется.

Проверьте уведомления в правом верхнем углу портал Azure или перейдите к группе ресурсов, содержащей учетную запись службы автоматизации, и выберите **развертывания** в разделе **Параметры** , чтобы просмотреть неудачное развертывание. Дополнительные сведения о политике Azure см. в статье [Обзор политики Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Сценарий: ошибки при попытке отменить связь с рабочей областью

#### <a name="issue"></a>Проблема

При попытке удалить связь с рабочей областью появляется следующее сообщение об ошибке:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Причина

Эта ошибка возникает, когда в рабочей области Log Analytics все еще есть решения, зависящие от учетной записи службы автоматизации и связанной с Log Analytics рабочей областью.

### <a name="resolution"></a>Решение

Если вы используете следующие решения, удалите их из рабочей области:

* Управление обновлениями
* Отслеживание изменений и инвентаризация
* Запуск и остановка виртуальных машин в нерабочее время

После удаления решений можно удалить связь с рабочей областью. Важно удалить все существующие артефакты из этих решений из рабочей области и учетной записи службы автоматизации:

* Для Управление обновлениями удалите **развертывания обновлений (расписания)** из учетной записи службы автоматизации.
* Для запуск и остановка виртуальных машин в нерабочее время удалите все блокировки на компоненты решения в учетной записи службы автоматизации в разделе **Параметры** > **блокировки**. Дополнительные сведения см. [в статье удаление запуск и остановка виртуальных машин в нерабочее время решения](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics для ошибок расширения Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Установка агента Log Analytics для расширения Windows может завершиться сбоем по ряду причин. В следующем разделе описываются проблемы адаптации, которые могут вызвать сбои во время развертывания расширения Log Analytics Agent для Windows.

>[!NOTE]
>Log Analytics агент для Windows — это имя, используемое в настоящее время в службе автоматизации Azure для Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Сценарий: Исключение во время запроса WebClient

Log Analytics для расширения Windows на виртуальной машине не удается связаться с внешними ресурсами, и развертывание завершается сбоем.

#### <a name="issue"></a>Проблема

Ниже приведены примеры возвращаемых сообщений об ошибках.

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Причина

Ниже перечислены некоторые возможные причины этой ошибки.

* Прокси-сервер, настроенный на виртуальной машине, разрешает только определенные порты.
* Настройки брандмауэра, которые блокируют доступ к запрашиваемым адресам и портам.

#### <a name="resolution"></a>Решение

Убедитесь в том, что у вас открыты порты и адреса, которые необходимы для сеанса связи. Список портов и адресов см. в разделе [планирование сети](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Сценарий: сбой установки из-за временных проблем среды

Не удалось установить расширение Log Analytics для Windows во время развертывания из-за другой установки или действия, блокирующего установку.

#### <a name="issue"></a>Проблема

Ниже приведены примеры сообщений об ошибках, которые могут быть возвращены:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Причина

Ниже перечислены некоторые возможные причины этой ошибки.

* Выполняется другая установка.
* Система активируется для перезагрузки во время развертывания шаблона.

#### <a name="resolution"></a>Решение

Эта ошибка является временной по своей природе. Чтобы установить расширение, необходимо повторить развертывание.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Сценарий: время ожидания установки истекло

Установка Log Analytics для расширения Windows не завершена из-за истечения времени ожидания.

#### <a name="issue"></a>Проблема

Ниже приведен пример сообщения об ошибке, которое может быть возвращено:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Причина

Этот тип ошибки возникает из-за высокой нагрузки на виртуальную машину во время установки.

### <a name="resolution"></a>Решение

Попробуйте установить расширение Log Analytics Agent для Windows, если виртуальная машина находится под низкой нагрузкой.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите проблему здесь или вы не можете решить проблему, попробуйте использовать один из следующих каналов для получения дополнительной поддержки:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с помощью официальной учетной записи Microsoft Azure для улучшения качества взаимодействия с [@AzureSupport](https://twitter.com/azuresupport)клиентами. Служба поддержки Azure подключается к сообществу Azure для получения ответов, поддержки и экспертов.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/)и выберите **получить поддержку**.
