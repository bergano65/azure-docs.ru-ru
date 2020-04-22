---
title: Устранение проблем с решениями Start/Stop VMs в нерабочее время
description: В этой статье содержится информация о устранении неполадок в решении Start/Stop VM.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679159"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Устранение неполадок с решением запуска и остановки виртуальных машин в нерабочее время

В этой статье содержится информация о проблемах устранения неполадок, возникающих при работе с Решениями Start/Stop VMs в нерабочее время.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Сценарий: Решение Start/Stop VMs в нерабочее время не может должным образом развернуть

### <a name="issue"></a>Проблемы

Развертывание [решения для запуска и остановки виртуальных машин в нерабочее время](../automation-solution-vm-management.md) приводит к одной из перечисленных ниже ошибок.

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Причина:

Развертывание может вывести из строя по одной из следующих причин:

1. В выбранном регионе уже имеется учетная запись Автоматизация с таким же названием.
2. Политика отключает развертывание внее времени в нерабочее время.
3. `Microsoft.Insights` `Microsoft.Automation` Тип `Microsoft.OperationsManagement`ресурса не зарегистрирован.
4. Рабочее пространство анализа журналов заблокировано.
5. У вас есть устаревшая версия модулей AzureRM или решения Start/Stop VMs в нерабочее время.

### <a name="resolution"></a>Решение

Просмотрите следующие исправления для потенциальных решений вашей проблемы:

* Учетные записи службы автоматизации должны быть уникальными в пределах региона Azure, даже если они находятся в разных группах ресурсов. Проверьте существующие учетные записи автоматизации в целевом регионе.
* Существующая политика предотвращает развертывание ресурса, необходимого для развертывания решения Start/Stop VMs в нерабочее время. Перейдите к своим назначениям политики на портале Azure и проверьте, есть ли у вас назначение политики, запрещающее развертывание этого ресурса. Дополнительные сведения см. в статье [Ошибка RequestDisallowedByPolicy с политикой ресурсов Azure](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Для развертывания решения Start/Stop VMs подписка должна быть зарегистрирована в следующих областях имен ресурсов Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Просмотреть [ошибки Resolve для регистрации поставщика ресурсов,](../../azure-resource-manager/templates/error-register-resource-provider.md) чтобы узнать больше об ошибках при регистрации поставщиков.
* Если у вас есть блокировка в рабочей области Log Analytics, перейдите в свою рабочую область на портале Azure и удалите все блокировки в ресурсе.
* Если приведенные выше резолюции не решают вашу проблему, следуйте инструкциям в рамках [обновления решения](../automation-solution-vm-management.md#update-the-solution) для повторного развертывания решения Start/Stop.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Сценарий: Все ВМ не могут начаться или остановиться

### <a name="issue"></a>Проблемы

Вы настроили решения Start/Stop VMs в нерабочее время, но оно не начинает и не останавливает все внештатные.

### <a name="cause"></a>Причина:

Ошибка может быть вызвана одной из следующих причин:

1. Расписание настроено неправильно.
2. Учетная запись Run As может быть настроена неправильно.
3. Запуск книги, возможно, столкнулись с ошибками.
4. ВМ могли быть исключены.

### <a name="resolution"></a>Решение

Просмотрите следующий список потенциальных решений вашей проблемы:

* Убедитесь, что вы правильно настроили расписание для решения Start/Stop VMs в нерабочее время. Сведения о настройке расписания см. в [этой статье](../automation-schedules.md).

* Проверьте [потоки рабочих мест,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) чтобы найти любые ошибки. Ищите работу из одного из следующих runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic;**
  * **ScheduledStartStop_Child_Classic;**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Убедитесь, что ваша [учетная запись Run As](../manage-runas-account.md) имеет соответствующие разрешения на вс-ми, которые вы пытаетесь запустить или остановить. Чтобы узнать, как проверить разрешения на [Quickstart: View roles assigned to a user using the Azure portal](../../role-based-access-control/check-access.md)ресурсе, см. Вам нужно будет предоставить идентификатор приложения для основного сервиса, используемого учетной записью Run As. Вы можете получить это значение, перейдя на свой аккаунт Automation на портале Azure, выбрав **Run в качестве учетных записей** в **настройках учетных записей**и нажав на соответствующую учетную запись Run As.

* Виртуальные машины не могут быть запущены или остановлены, если они были явно исключены. Исключенные ВМ установлены в `External_ExcludeVMNames` переменной в учетной записи Автоматизации, на которую развернуто решение. Ниже приводится пример, как можно заразить это значение с помощью PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Сценарий: Некоторые из моих vMs не могут начать или остановить

### <a name="issue"></a>Проблемы

Вы настроили решение Start/Stop VMs в нерабочее время, но оно не начинаети и не останавливают некоторые из накидных vMs.

### <a name="cause"></a>Причина:

Ошибка может быть вызвана одной из следующих причин:

1. В сценарии последовательности тег может отсутствовать или неверным.
2. VM может быть исключен.
3. Учетная запись Run As может не иметь достаточного количества разрешений на VM.
4. У VM может быть проблема, которая остановила его от запуска или остановки.

### <a name="resolution"></a>Решение

Ниже приведен список возможных решений для вашей проблемы и указано, где следует искать источник неполадки.

* При использовании [последовательности сценария](../automation-solution-vm-management.md) Start/Stop VMs в нерабочее время решения, вы должны убедиться, что каждый VM вы хотите начать или остановить имеет надлежащий тег. Виртуальным машинам, которые нужно запустить, должен быть присвоен тег `sequencestart`, а виртуальным машинам, которые нужно остановить, — тег `sequencestop`. Для обоих тегов требуется положительное целое значение. Для поиска всех виртуальных машин с тегами и значений тегов можно использовать запрос, аналогичный приведенному ниже.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* ВМ не могут быть запущены или остановлены, если они явно исключены. Исключенные ВМ установлены в `External_ExcludeVMNames` переменной в учетной записи Автоматизации, на которую развернуто решение. Ниже приводится пример, как можно заразить это значение с помощью PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Для запуска и остановки ВМ учетная запись Run As для учетной записи Automation должна иметь соответствующие разрешения на VM. Чтобы узнать, как проверить разрешения на [Quickstart: View roles assigned to a user using the Azure portal](../../role-based-access-control/check-access.md)ресурсе, см. Вам нужно будет предоставить идентификатор приложения для основного сервиса, используемого учетной записью Run As. Вы можете получить это значение, перейдя на свой аккаунт Automation на портале Azure, выбрав **Run в качестве учетных записей** в **настройках учетной записи** и нажав на соответствующую учетную запись Run As.

* Если у VM возникла проблема с запуском или размещением сделки, то может возникнуть проблема с самим VM. Например, при попытке отключения VM применяется обновление, зависает служба и многое другое. Перейдите к ресурсу виртуальной машины и проверьте, нет ли в **журналах действий** записей об ошибках. Вы также можете попытаться войти в VM, чтобы увидеть, есть ли ошибки в журналах событий. Чтобы узнать больше о устранении неполадок вашего VM, [см.](../../virtual-machines/troubleshooting/index.yml)

* Проверьте [потоки рабочих мест,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) чтобы найти любые ошибки. На портале перейдите на свой аккаунт Автоматизации и выберите **вакансии** под **автоматизацией процессов.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Сценарий: Мой пользовательский runbook не может начать или остановить мои VMs

### <a name="issue"></a>Проблемы

Вы создали пользовательский модуль runbook или скачали его из коллекции PowerShell, но он не работает должным образом.

### <a name="cause"></a>Причина:

Там может быть много причин для отказа. Перейдите на свой аккаунт Автоматизации на портале Azure и выберите **вакансии** под **автоматизацией процессов.** На странице Задания просмотрите, нет ли сведений о сбоях заданий runbook.

### <a name="resolution"></a>Решение

Рекомендуется:

* Используйте [решения Start/Stop VMs в нерабочее время,](../automation-solution-vm-management.md) чтобы начать и остановить ВМ в Azure Automation. Разработчик этого решения — корпорация Майкрософт. 

* Имейте в виду, что корпорация Майкрософт не поддерживает пользовательские рунбуки. Вы можете найти решение для пользовательского runbook из [runbook устранения неполадок.](runbooks.md) Проверьте [потоки рабочих мест,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) чтобы найти любые ошибки. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Сценарий: VMs не запускать или останавливаться в правильной последовательности

### <a name="issue"></a>Проблемы

Виртуальные машины, настроенные вами в решении, не запускаются или не останавливаются в правильной очередности.

### <a name="cause"></a>Причина:

Эта проблема вызвана неправильной пометкой на ВМ.

### <a name="resolution"></a>Решение

Выполните следующие действия, чтобы убедиться, что решение настроено правильно.

1. Убедитесь, что всем виртуальным машинам, которые нужно запустить, присвоен тег `sequencestart`, а тем, которые нужно остановить, — тег `sequencestop`. Этим тегам в качестве значения требуется положительное целое число. Виртуальные машины обрабатываются по возрастанию на основе этого значения.
2. Убедитесь, что группы ресурсов для виртуальных машин, которые нужно запустить, указаны в переменной `External_Start_ResourceGroupNames`, а для тех виртуальных машин, которые нужно остановить, — в переменной `External_Stop_ResourceGroupNames`.
3. Проверьте свои изменения, выполнить `SequencedStartStop_Parent` runbook `WHATIF` с параметром, установленным для true, чтобы просмотреть ваши изменения.
4. Для получения дополнительной информации об использовании решения для запуска и остановки ВМ в [последовательности](../automation-solution-vm-management.md)см.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Сценарий: Start/Stop VMs во время работы в нерабочее время не удается с 403 запрещенной ошибкой

### <a name="issue"></a>Проблемы

Вы находите задания, `403 forbidden` которые не смогли с ошибкой для Start/Stop VMs во время нерабочего времени решения runbooks.

### <a name="cause"></a>Причина:

Эта проблема может быть вызвана неправильно настроенной или просроченной учетной записью Run As. Это также может быть из-за недостаточных разрешений на ресурсы VM учетной записью Run As.

### <a name="resolution"></a>Решение

Чтобы убедиться, что ваша учетная запись Run As настроена должным образом, перейдите на свой аккаунт Автоматизации на портале Azure и выберите **Run в качестве учетных записей** в **настройках учетной записи.** Если учетная запись Run As неправильно настроена или просрочена, статус отображает условие.

Если ваша учетная запись Run As настроена неправильно, следует удалить и воссоздать учетную запись Run As. Смотрите [Управление Azure Автоматизация Run Как счета](../manage-runas-account.md).

Если срок действия сертификата истек для учетной записи Run As, смотрите шаги в [продлении сертификата, подписанном self- для](../manage-runas-account.md#cert-renewal) продления сертификата.

При наличии отсутствующих [Quickstart: View roles assigned to a user using the Azure portal](../../role-based-access-control/check-access.md)разрешений см. Вы должны предоставить идентификатор приложения для основного сервиса, используемого учетной записью Run As. Вы можете получить это значение, перейдя на свой аккаунт Automation на портале Azure, выбрав **Run в качестве учетных записей** в **настройках учетных записей**и нажав на соответствующую учетную запись Run As.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Сценарий: Моя проблема не указана выше

### <a name="issue"></a>Проблемы

Вы испытываете проблему или неожиданный результат при использовании решения Start/Stop VMs в нерабочее время, которое не перечислено на этой странице.

### <a name="cause"></a>Причина:

Зачастую ошибки могут возникать из-за использования старых и устаревших версий решения.

> [!NOTE]
> Решение Start/Stop VMs в нерабочее время было протестировано с помощью модулей Azure, импортируемых в учетную запись автоматизации при развертывании решения. В настоящее время решение не работает с новыми версиями модуля Azure. Это влияет только на учетную запись автоматизации, которую вы используете для запуска решения Start/Stop VMs в нерабочее время. В других учетных записях автоматизации по-прежнему можно использовать новые версии модуля Azure, описанные в [модулях Azure PowerShell в Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Решение

Для устранения многих ошибок рекомендуется удалить и [обновить решение Start/Stop VMs в нерабочее время.](../automation-solution-vm-management.md#update-the-solution) Кроме того, вы можете проверить [потоки задания,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) чтобы найти любые ошибки. 

## <a name="next-steps"></a>Следующие шаги

Если вы не видите проблему выше или не можете решить вашу проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**