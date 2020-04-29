---
title: Устранение неполадок Управление обновлениями службы автоматизации Azure
description: Узнайте, как устранять неполадки и устранять проблемы с решением Управление обновлениями в службе автоматизации Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91ecff311b8820d3b97e1de0e4b4e87c150e749b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678923"
---
# <a name="troubleshoot-issues-with-the-update-management-solution"></a>Устранение неполадок в решении Управление обновлениями

В этой статье обсуждаются проблемы, с которыми можно столкнуться при использовании решения Управление обновлениями. Существует средство устранения неполадок агента для гибридного агента Runbook Worker, чтобы определить базовую проблему. Дополнительные сведения о средстве устранения неполадок см. в статьях [Устранение неполадок агента обновления Windows](update-agent-issues.md) и [Устранение неполадок агента обновления Linux](update-agent-issues-linux.md). Сведения о других проблемах адаптации см. в разделе [Устранение неполадок при адаптации решения](onboarding.md).

>[!NOTE]
>Если при подключении решения к виртуальной машине обнаружены проблемы, проверьте журнал **Operations Manager** в разделе **журналы приложений и служб** на локальном компьютере. Найдите события с ИД события 4502 и сведения о событии, `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`содержащие.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Сценарий. появляется сообщение об ошибке "не удалось включить решение обновления"

### <a name="issue"></a>Проблема

При попытке включить решение Управление обновлениями в учетной записи службы автоматизации появляется следующее сообщение об ошибке:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Причина

Эта ошибка может возникать по следующим причинам.

* Требования к сетевому брандмауэру для агента Log Analytics могут быть настроены неправильно. Такая ситуация может привести к сбою агента при разрешении URL-адресов DNS.

* Нацеливание решения настроено неправильно, и компьютер не получает обновлений должным образом.

* Вы также можете заметить, что компьютер отображается `Non-compliant` в состоянии **соответствует требованиям**. В то же время агент **Desktop Analytics** сообщает агенту как `Disconnected`.

### <a name="resolution"></a>Решение

* Запустите средство устранения неполадок для [Windows](update-agent-issues.md#troubleshoot-offline) или [Linux](update-agent-issues-linux.md#troubleshoot-offline)в зависимости от операционной системы.

* Перейдите к разделу [планирование сети](../automation-hybrid-runbook-worker.md#network-planning) , чтобы узнать, какие адреса и порты должны быть разрешены для работы Управление обновлениями.  

* Перейдите к разделу [планирование сети](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) , чтобы узнать, какие адреса и порты должны быть разрешены для работы агента log Analytics.

* Проверьте наличие проблем с конфигурацией области. [Конфигурация области](../automation-onboard-solutions-from-automation-account.md#scope-configuration) определяет, какие компьютеры настраиваются для решения. Если компьютер отображается в рабочей области, но не на портале Управление обновлениями * *, необходимо задать конфигурацию области для целевых компьютеров. Дополнительные сведения о конфигурации области см. в статье подключение [компьютеров в рабочей области](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Удалите конфигурацию рабочей роли, выполнив действия, описанные в статье [Удаление гибридной рабочей роли Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Сценарий: замененное обновление указано как отсутствующее в Управление обновлениями

### <a name="issue"></a>Проблема

Старые обновления отображаются для учетной записи службы автоматизации, даже если они были заменены. Заменяемое обновление является ненужным, так как доступно более позднее обновление, устраняющее ту же уязвимость. Управление обновлениями игнорирует заменяемое обновление и делает его неприменимым в пользу замены заменяемого обновления. Сведения о связанных проблемах см. в разделе [обновление заменено](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Причина

Замененные обновления не были правильно отмечены как отклоненные, чтобы их можно было считать неприменимыми.

### <a name="resolution"></a>Решение

Если заменяемое обновление преобразуется в 100% неприменимо, следует изменить состояние утверждения этого обновления на `Declined`. Чтобы изменить состояние утверждения для всех обновлений, выполните следующие действия.

1. В учетной записи службы автоматизации выберите **Управление обновлениями** , чтобы просмотреть состояние компьютера. См. раздел [Просмотр оценок обновлений](../manage-update-multi.md#view-an-update-assessment).

2. Проверьте заменяемое обновление, чтобы убедиться в том, что его 100% неприменимо. 

3. Пометьте обновление как отклоненное, если у вас нет вопроса об обновлении. 

4. Выберите **Компьютеры** и в столбце **соответствие** выполните принудительное повторное сканирование на соответствие. См. раздел [Управление обновлениями для нескольких компьютеров](../manage-update-multi.md).

5. Повторите описанные выше действия для других заменяемых обновлений.

6. Запустите мастер очистки, чтобы удалить файлы из отклоненных обновлений. 

7. Для Windows Server Update Services (WSUS) вручную очистите все заменяемые обновления, чтобы обновить инфраструктуру.

8. Повторяйте эту процедуру регулярно, чтобы устранить проблему с отображением и максимально сокращать объем дискового пространства, используемого для управления обновлениями.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Сценарий: компьютеры не отображаются на портале в разделе Управление обновлениями

### <a name="issue"></a>Проблема

Ваши компьютеры имеют следующие признаки:

* Компьютер отображается `Not configured` из Управление обновлениями представления виртуальной машины.

* Ваши компьютеры отсутствуют в Управление обновлениями представлении учетной записи службы автоматизации Azure.

* У вас есть компьютеры, которые `Not assessed` отображаются в соответствии с **требованиями**. Однако вы видите данные пульса в журналах Azure Monitor для гибридной рабочей роли Runbook, но не для Управление обновлениями.

### <a name="cause"></a>Причина

Эта проблема может быть вызвана проблемами локальной конфигурации или неправильно настроенной конфигурацией области. Возможные причины:

* Возможно, потребуется повторно зарегистрировать и переустановить гибридную рабочую роль Runbook.

* Возможно, вы определили квоту в рабочей области, которая была достигнута, и это помешает дополнительному хранению данных.

### <a name="resolution"></a>Решение

1. Запустите средство устранения неполадок для [Windows](update-agent-issues.md#troubleshoot-offline) или [Linux](update-agent-issues-linux.md#troubleshoot-offline)в зависимости от операционной системы.

2. Убедитесь, что компьютер сообщает о правильной рабочей области. Инструкции по проверке этого аспекта см. в разделе [Проверка подключения агента к log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Также убедитесь, что эта Рабочая область связана с учетной записью службы автоматизации Azure. Для подтверждения перейдите в учетную запись службы автоматизации и выберите **связанная Рабочая область** в разделе **связанные ресурсы**.

3. Убедитесь, что компьютеры отображаются в рабочей области Log Analytics, связанной с учетной записью службы автоматизации. Выполните следующий запрос в рабочей области Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Если компьютер не отображается в результатах запроса, он не был недавно возвращен. Возможно, существует ошибка локальной конфигурации, и необходимо [переустановить агент](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Если компьютер отображается в результатах запроса, проверьте наличие проблем с конфигурацией области. [Конфигурация области](../automation-onboard-solutions-from-automation-account.md#scope-configuration) определяет, какие машины настроены для решения. 

6. Если компьютер отображается в рабочей области, но не в Управление обновлениями, необходимо настроить конфигурацию области для целевого компьютера. Сведения о том, как это сделать, см. в статье подключение [компьютеров в рабочей области](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

7. В рабочей области выполните этот запрос.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. При получении `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` результата была достигнута квота, определенная в рабочей области, которая остановила сохранение данных. В рабочей области перейдите в раздел **Управление томами данных** в разделе **использование и оценка затрат**, а затем измените или удалите квоту.

9. Если проблемы по-прежнему не удается решить, выполните действия, описанные в статье [развертывание гибридной рабочей роли Runbook Windows](../automation-windows-hrw-install.md) для переустановки гибридной рабочей роли для Windows. Для Linux выполните действия, описанные в статье [развертывание гибридной рабочей роли Runbook для Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Сценарий: не удалось зарегистрировать поставщик ресурсов службы автоматизации для подписок

### <a name="issue"></a>Проблема

При работе с решениями в учетной записи службы автоматизации возникает следующая ошибка:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Причина

Поставщик ресурсов службы автоматизации не зарегистрирован в подписке.

### <a name="resolution"></a>Решение

Чтобы зарегистрировать поставщик ресурсов службы автоматизации, выполните следующие действия в портал Azure.

1. В списке служб Azure в нижней части портала выберите **все службы**, а затем щелкните **подписки** в группе "Общие".

2. Выберите свою подписку.

3. В разделе **Параметры**выберите **поставщики ресурсов**.

4. Убедитесь, что в списке поставщиков ресурсов зарегистрирован поставщик ресурсов Microsoft. Automation.

5. Если он отсутствует в списке, зарегистрируйте поставщик Microsoft. Automation, выполнив действия, описанные в разделе [Устранение ошибок для регистрации поставщика ресурсов](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Сценарий: запланированное обновление с динамическим расписанием, в котором пропущены некоторые компьютеры

### <a name="issue"></a>Проблема

Компьютеры, включенные в предварительную версию обновления, не отображаются в списке компьютеров, исправленных во время запланированного запуска.

### <a name="cause"></a>Причина

Эта проблема может быть вызвана одной из следующих причин:

* Подписки, определенные в области в динамическом запросе, не настроены для зарегистрированного поставщика ресурсов автоматизации.

* Компьютеры не были доступны или не имеют подходящих тегов при выполнении расписания.

### <a name="resolution"></a>Решение

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Подписки, не настроенные для зарегистрированного поставщика ресурсов автоматизации

Если ваша подписка не настроена для поставщика ресурсов службы автоматизации, вы не сможете запрашивать или получать сведения на компьютерах в этой подписке. Чтобы проверить регистрацию подписки, выполните следующие действия.

1. В [портал Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)получите доступ к списку служб Azure.

2. Выберите **все службы**, а затем щелкните **подписки** в группе "Общие". 

3. Найдите подписку, определенную в области развертывания.

4. В разделе **Параметры**выберите **поставщики ресурсов**.

5. Убедитесь, что поставщик ресурсов Microsoft. Automation зарегистрирован.

6. Если он отсутствует в списке, зарегистрируйте поставщик Microsoft. Automation, выполнив действия, описанные в разделе [Устранение ошибок для регистрации поставщика ресурсов](/azure/azure-resource-manager/resource-manager-register-provider-errors).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Недоступные компьютеры или неправильно помеченные тегами при выполнении расписания

Используйте следующую процедуру, если ваша подписка настроена для поставщика ресурсов автоматизации, но выполнение расписания обновления с указанными [динамическими группами](../automation-update-management-groups.md) пропустило некоторые компьютеры.

1. В портал Azure откройте учетную запись службы автоматизации и выберите **Управление обновлениями**.

2. Проверьте [журнал Управление обновлениями](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) , чтобы определить точное время выполнения развертывания обновления. 

3. Для компьютеров, которые вы считаете незамеченными Управление обновлениями, используйте граф ресурсов Azure (ARG) для [выявления изменений компьютера](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. Поиск изменений в значительном периоде, например за один день, до запуска развертывания обновления.

5. Проверьте результаты поиска на наличие системных изменений, таких как удаление или обновление изменений, на компьютерах в этом периоде. Эти изменения могут изменить состояние или Теги компьютера, чтобы компьютеры не были выбраны в списке компьютеров при развертывании обновлений.

6. При необходимости измените параметры компьютеров и ресурсов, чтобы устранить проблемы с состоянием компьютера или тегами.

7. Повторно запустите расписание обновления, чтобы убедиться, что развертывание с указанными динамическими группами включает все компьютеры.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Сценарий: ожидаемые компьютеры не отображаются в предварительной версии для динамической группы

### <a name="issue"></a>Проблема

Виртуальные машины для выбранных областей динамической группы не отображаются в списке предварительной версии портал Azure. Этот список состоит из всех компьютеров, полученных запросом ARG для выбранных областей. Области фильтруются для компьютеров, на которых установлены гибридные рабочие роли Runbook, для которых у вас есть разрешения на доступ. 

### <a name="cause"></a>Причина
 
Ниже приведены возможные причины этой проблемы.

* У вас нет правильного доступа к выбранным областям.
* Запрос ARG не извлекает ожидаемые компьютеры.
* Гибридная Рабочая роль Runbook не установлена на компьютерах.

### <a name="resolution"></a>Решение 

#### <a name="incorrect-access-on-selected-scopes"></a>Неверный доступ к выбранным областям

В портал Azure отображаются только те компьютеры, для которых у вас есть доступ на запись в заданной области. Если у вас нет правильного доступа к области, см. раздел [учебник. предоставление пользователю доступа к ресурсам Azure с помощью RBAC и портал Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>Запрос ARG не возвращает ожидаемые компьютеры

Выполните следующие действия, чтобы выяснить, правильно ли работают запросы.

1. Выполните запрос ARG, как показано ниже в колонке обозреватель графа ресурсов в портал Azure. Этот запрос имитирует фильтры, выбранные при создании динамической группы в Управление обновлениями. См. раздел [Использование динамических групп с Управление обновлениями](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Например:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. Проверьте, указаны ли запрашиваемые компьютеры в результатах запроса. 

3. Если компьютеры отсутствуют в списке, вероятно, есть проблемы с фильтром, выбранным в динамической группе. При необходимости измените конфигурацию группы.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Гибридная Рабочая роль Runbook не установлена на компьютерах

Компьютеры отображаются в результатах запроса ARG, но по-прежнему не отображаются в предварительной версии динамической группы. В этом случае компьютеры могут быть не обозначены как гибридные рабочие роли и, таким образом, не могут запускать задания службы автоматизации Azure и Управление обновлениями. Чтобы убедиться, что компьютеры, которые вы ожидаете просматривать, настроены как гибридные рабочие роли Runbook, выполните следующие действия.

1. В портал Azure перейдите к учетной записи службы автоматизации для компьютера, который отображается неправильно.

2. Выберите **группы гибридных рабочих ролей** в разделе **Автоматизация процессов**.

3. Выберите вкладку **группы гибридных рабочих ролей системы** .

4. Проверьте наличие гибридной рабочей роли для этого компьютера.

5. Если компьютер не настроен как гибридная Рабочая роль, выполните корректировки с помощью инструкций по [автоматизации ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Присоедините компьютер к группе гибридных рабочих ролей Runbook.

7. Повторите описанные выше действия для всех компьютеров, которые не отображались в предварительной версии.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Сценарий: компоненты для Управление обновленияминого решения, тогда как виртуальная машина будет отображаться как настроенная

### <a name="issue"></a>Проблема

Приведенное ниже сообщение на виртуальной машине не исчезает в течение 15 минут после подключения:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Причина

Эта ошибка может возникать по следующим причинам.

* Связь с учетной записью службы автоматизации блокируется.

* Существует повторяющееся имя компьютера с разными идентификаторами исходного компьютера. Такая ситуация возникает, когда виртуальная машина с определенным именем компьютера создается в разных группах ресурсов и сообщается одной рабочей области агента логистики в подписке.

* Подключаемый образ виртуальной машины может быть создан на клонированном компьютере, который не был подготовлен с помощью программы подготовки системы (Sysprep) с установленным агентом Log Analytics для Windows.

### <a name="resolution"></a>Решение

Чтобы определить точную проблему с виртуальной машиной, выполните следующий запрос в Log Analytics рабочей области, связанной с учетной записью службы автоматизации.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Обмен данными с учетной записью службы автоматизации заблокирован

Перейдите к разделу [планирование сети](../automation-update-management.md#ports) , чтобы узнать, какие адреса и порты должны быть разрешены для работы Управление обновлениями.

#### <a name="duplicate-computer-name"></a>Повторяющееся имя компьютера

Переименуйте виртуальные машины, чтобы обеспечить уникальность имен в их среде.

#### <a name="onboarded-image-from-cloned-machine"></a>Подключенное изображение с клонированного компьютера

Если вы используете клонированный образ, разные имена компьютеров имеют одинаковый идентификатор исходного компьютера. В данном случае:

1. В рабочей области Log Analytics Удалите виртуальную машину из сохраненного поиска для конфигурации `MicrosoftDefaultScopeConfig-Updates` области, если она отображается. Сохраненные поисковые запросы хранятся на вкладке **Общие** рабочей области.

2. Выполните следующий командлет.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Запустите `Restart-Service HealthService` , чтобы перезапустить службу работоспособности. Эта операция повторно создает ключ и создает новый UUID.

4. Если такой подход не работает, сначала запустите Sysprep на образе, а затем установите MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Сценарий: при создании развертывания обновлений для компьютеров в другом клиенте Azure возникает ошибка связанной подписки

### <a name="issue"></a>Проблема

При попытке создать развертывание обновления для компьютеров в другом клиенте Azure возникает следующая ошибка:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Причина

Эта ошибка возникает при создании развертывания обновлений с виртуальными машинами Azure в другом клиенте, который входит в развертывание обновлений.

### <a name="resolution"></a>Решение

Чтобы получить запланированные элементы, используйте следующее решение. Для создания расписания можно использовать командлет [New-азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) с `ForUpdateConfiguration` параметром. Затем используйте командлет [New-азаутоматионсофтвареупдатеконфигуратион](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) и передайте компьютеры в другом клиенте в `NonAzureComputer` параметр. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Сценарий: необъясненные перезагрузки

### <a name="issue"></a>Проблема

Несмотря на то, что параметр **управления перезагрузкой** имеет значение **никогда не**перезагружаться, компьютеры по-прежнему перезапускаются после установки обновлений.

### <a name="cause"></a>Причина

Центр обновления Windows могут быть изменены несколькими разделами реестра, с помощью которых можно изменить поведение при перезагрузке.

### <a name="resolution"></a>Решение

Изучите разделы реестра, перечисленные в разделе [настройка Автоматическое обновление, изменив реестр](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) и [разделы реестра, используемые для управления перезагрузкой](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , чтобы убедиться, что компьютеры настроены должным образом.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Сценарий: на компьютере отображается сообщение "не удалось запустить" в развертывании обновлений

### <a name="issue"></a>Проблема

На компьютере отображается `Failed to start` состояние. При просмотре конкретных сведений о компьютере отображается следующая ошибка:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Причина

Ошибка может быть вызвана одной из следующих причин:

* Компьютер больше не существует.
* Компьютер отключен и недоступен.
* Компьютер имеет неполадки с сетевым подключением, поэтому Гибридная Рабочая роль на компьютере недоступна.
* Произошло обновление для MMA, которое изменило идентификатор исходного компьютера.
* Выполнение обновления было отрегулировано, если достигнуто ограничение в 2000 параллельных заданий в учетной записи службы автоматизации. Каждое развертывание считается заданием, а каждый компьютер в развертывании обновлений учитывается как задание. Любое другое задание службы автоматизации или развертывание обновления, выполняющееся в вашей учетной записи службы автоматизации, учитывает ограничение на число параллельных заданий.

### <a name="resolution"></a>Решение

При необходимости используйте [динамические группы](../automation-update-management-groups.md) для развертываний обновлений. Кроме того, можно выполнить следующие действия.

1. Убедитесь, что компьютер по-прежнему существует и доступен. 
2. Если компьютер не существует, измените развертывание и удалите компьютер.
3. Список портов и адресов, необходимых для Управление обновлениями, см. в разделе [планирование сети](../automation-update-management.md#ports) , а затем убедитесь, что компьютер соответствует этим требованиям.
4. Проверьте подключение к гибридной рабочей роли Runbook с помощью гибридного средства устранения неполадок агента рабочей роли Runbook. Для дополнительных сведений о средстве устранения неполадок см. [Общие сведения о результатах проверки агента в службе "Управление обновлениями"](update-agent-issues.md).
5. Выполните следующий запрос в Log Analytics, чтобы найти компьютеры в среде, для которых изменился идентификатор исходного компьютера. Найдите компьютеры с одинаковым `Computer` значением, но с другим `SourceComputerId` значением.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. После обнаружения затронутых компьютеров измените развертывания обновлений, предназначенные для этих компьютеров, а затем удалите и прочтите их, `SourceComputerId` чтобы они отражали правильное значение.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Сценарий: обновления устанавливаются без развертывания

### <a name="issue"></a>Проблема

При регистрации компьютера Windows в Управление обновлениями вы увидите обновления, установленные без развертывания.

### <a name="cause"></a>Причина

В Windows обновления устанавливаются автоматически, как только они станут доступны. Такое поведение может вызвать путаницу, если вы не запланировали развертывание обновления на компьютере.

### <a name="resolution"></a>Решение

Раздел `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` реестра по умолчанию имеет значение 4: `auto download and install`.

Для Управление обновлениями клиентов рекомендуется задать для этого раздела значение 3: `auto download but do not auto install`.

Дополнительные сведения см. в разделе [настройка Автоматическое обновление](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Сценарий: компьютер уже зарегистрирован на другой учетной записи

### <a name="issue"></a>Проблема

Отображается следующее сообщение об ошибке:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Причина

Компьютер уже подключен к другой рабочей области для Управление обновлениями.

### <a name="resolution"></a>Решение

1. Выполните действия, описанные в разделе Компьютеры, которые [не отображаются на портале, в разделе Управление обновлениями](#nologs) , чтобы убедиться, что компьютер сообщает о правильной рабочей области.
2. Очистите артефакты на компьютере, [удалив гибридную группу Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), и повторите попытку.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Сценарий: компьютеру не удается связаться со службой

### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Причина

Прокси-сервер, шлюз или брандмауэр могут блокировать сетевую связь. 

### <a name="resolution"></a>Решение

Проверьте сеть и убедитесь, что разрешены соответствующие порты и адреса. Список портов и адресов, необходимых для Управление обновлениями и гибридных рабочих ролей Runbook, см. в статье [требования к сети](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Сценарий: не удалось создать самозаверяющий сертификат

### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Причина

Гибридной рабочей роли Runbook не удалось создать самозаверяющий сертификат.

### <a name="resolution"></a>Решение

Убедитесь, что учетная запись System имеет доступ на чтение к папке **к:\програмдата\микрософт\крипто\рса** , и повторите попытку.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Сценарий: сбой запланированного обновления с ошибкой Маинтенанцевиндовексцеедед

### <a name="issue"></a>Проблема

Период обслуживания по умолчанию для обновлений составляет 120 минут. Период обслуживания можно увеличить до 6 часов или 360 минут.

### <a name="resolution"></a>Решение

Изменение всех неудачных развертываний обновлений и увеличение периода обслуживания.

Дополнительные сведения о периодах обслуживания см. в разделе [install updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Сценарий: компьютер отображается как "не оценено" и отображает исключение HRESULT

### <a name="issue"></a>Проблема

* У вас есть компьютеры, которые `Not assessed` отображаются в соответствии с **требованиями**, и вы видите сообщение об исключении под ними.
* На портале отобразится код ошибки HRESULT.

### <a name="cause"></a>Причина

Агент обновления (агент Центр обновления Windows в Windows; Диспетчер пакетов для дистрибутива Linux) настроен неправильно. Управление обновлениями полагается на агент обновления компьютера, чтобы предоставить необходимые обновления, состояние исправления и результаты развернутых исправлений. Без этих сведений Управление обновлениями не может правильно сообщить о необходимых или установленных исправлениях.

### <a name="resolution"></a>Решение

Попробуйте выполнить обновления локально на компьютере. Если эта операция завершается неудачно, это обычно означает ошибку конфигурации агента обновления.

Эта проблема часто возникает из-за проблем с конфигурацией сети и брандмауэром. Чтобы устранить эту ошибку, используйте следующие проверки.

* Для Linux проверьте соответствующую документацию, чтобы убедиться, что вы можете связаться с конечной точкой сети репозитория пакетов.

* Для Windows проверьте конфигурацию агента, указанную в списке [обновления, не скачиваются из конечной точки интрасети (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Если компьютеры настроены для Центр обновления Windows, убедитесь, что вы можете связаться с конечными точками, описанными в разделе [проблемы, связанные с HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Если компьютеры настроены для Windows Server Update Services (WSUS), убедитесь, что вы можете подключиться к серверу WSUS, настроенному с помощью [раздела реестра WUServer](/windows/deployment/update/waas-wu-settings).

Если отображается значение HRESULT, дважды щелкните исключение, отображаемое красным цветом, чтобы просмотреть сообщение об исключении целиком. Ознакомьтесь со следующей таблицей, чтобы получить возможные решения или Рекомендуемые действия.

|Исключение  |Разрешение или действие  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Найдите соответствующий код ошибки в [списке кодов ошибок Центра обновления Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list) , чтобы найти дополнительные сведения о причине исключения.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Они указывают на проблемы с сетевым подключением. Убедитесь, что компьютер имеет сетевое подключение к Управление обновлениями. Список требуемых портов и адресов см. в разделе [планирование сети](../automation-update-management.md#ports) .        |
|`0x8024001E`| Операция обновления не завершена из-за завершения работы службы или системы.|
|`0x8024002E`| Служба Центр обновления Windows отключена.|
|`0x8024402C`     | Если вы используете сервер WSUS, убедитесь, что для `WUServer` параметров реестра и `WUStatusServer` в разделе `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` реестра указан правильный сервер WSUS.        |
|`0x80072EE2`|Возникла неполадка с сетевым подключением или возникла неполадка при взаимодействии с настроенным сервером WSUS. Проверьте параметры WSUS и убедитесь, что служба доступна с клиента.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Убедитесь, что служба Центр обновления Windows (wuauserv) запущена и не отключена.        |
|`0x80070005`| Ошибка отказа в доступе может быть вызвана одной из следующих причин:<br> Зараженный компьютер<br> Неправильно настроены параметры Центр обновления Windows<br> Ошибка разрешения файла в папке%Виндир%\софтваредистрибутион<br> Недостаточно места на системном диске (C:).
|Другое универсальное исключение     | Выполните поиск в Интернете, чтобы получить возможные решения, и поработайте с локальной ИТ-поддержкой.         |

Просмотр файла **%виндир%\виндовсупдате.лог** также может помочь определить возможные причины. Дополнительные сведения о чтении журнала см. в разделе [чтение файла windowsupdate. log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Кроме того, можно загрузить и запустить [средство устранения неполадок центр обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , чтобы проверить наличие проблем с центр обновления Windows на компьютере.

> [!NOTE]
> Документация по [устранению неполадок центр обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) указывает на то, что она предназначена для использования на клиентах Windows, но она также работает в Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Сценарий: состояние сбоя возврата при выполнении обновления (Linux)

### <a name="issue"></a>Проблема

Запуск обновления начинается, но во время выполнения возникают ошибки.

### <a name="cause"></a>Причина

Возможные причины:

* Диспетчер пакетов неработоспособен.
* Агент обновления (WUA для Windows, дистрибутив для диспетчера пакетов для Linux) неправильно настроен.
* Определенные пакеты мешают установке исправлений на основе облака.
* Компьютер недоступен.
* Обновления имели зависимости, которые не были разрешены.

### <a name="resolution"></a>Решение

Если сбои возникают во время выполнения обновления после его успешного запуска, [Проверьте выходные данные задания](../manage-update-multi.md#view-results-of-an-update-deployment) на затронутом компьютере в ходе выполнения. Вы можете найти определенные сообщения об ошибках на ваших компьютерах, которые можно исследовать, и предпринять соответствующие действия. Для успешного обновления развертываний управлению обновлениями необходима работоспособность диспетчера пакетов.

Если конкретные исправления, пакеты или обновления отображаются непосредственно перед сбоем задания, можно попытаться [исключить](../automation-tutorial-update-management.md#schedule-an-update-deployment) эти элементы из следующего развертывания обновления. Сведения о сборе данных журнала из Центр обновления Windows см. в разделе [Центр обновления Windows файлы журнала](/windows/deployment/update/windows-update-logs).

Если не удается устранить проблему с исправлением, создайте копию файла **/Вар/ОПТ/Микрософт/омсажент/рун/аутоматионворкер/омсупдатемгмт.лог** и сохраните ее для устранения неполадок перед началом следующего развертывания обновления.

## <a name="patches-arent-installed"></a>Исправления не установлены

### <a name="machines-dont-install-updates"></a>Обновления не устанавливаются на компьютеры

Попробуйте выполнить обновление непосредственно на компьютере. Если компьютеру не удается применить обновления, просмотрите [список потенциальных ошибок в разделе Руководство по устранению неполадок](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Если обновления выполняются локально, попробуйте удалить и переустановить агент на компьютере, следуя указаниям в статье [Удаление виртуальной машины из Управление обновлениями](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Я знал, что доступны обновления, но они не отображаются как доступные на моих компьютерах

Это часто происходит, если компьютеры настроены на получение обновлений из WSUS или Microsoft Endpoint Configuration Manager, но WSUS и Configuration Manager не утвердили обновления.

Можно проверить, настроены ли на компьютерах службы WSUS и SCCM, перекрестно ссылаясь на `UseWUServer` раздел реестра на разделы реестра в [разделе Настройка автоматическое обновление, изменив раздел реестра этой статьи](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Если обновления не утверждены в WSUS, они не установлены. Вы можете проверить неодобренные обновления в Log Analytics, выполнив следующий запрос.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Обновления отображаются как установленные, но найти их на компьютере не удается

Обновления часто заменяются другими обновлениями. Дополнительные сведения см. в разделе [Обновление заменяется](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) в центр обновления Windows руководство по устранению неполадок.

### <a name="installing-updates-by-classification-on-linux"></a>Установка обновлений с учетом классификации на платформе Linux

При развертывании обновлений на Linux с учетом классификации (критические обновления и обновления системы безопасности) следует учитывать некоторые важные ограничения, особенно для CentOS. Эти ограничения описаны на [странице обзор Управление обновлениями](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 постоянно отсутствует

Статья базы знаний № 2267602 касается [обновления определений Защитника Windows](https://www.microsoft.com/wdsi/definitions). Он обновляется ежедневно.

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите проблему или не можете устранить проблему, воспользуйтесь одним из следующих каналов для получения дополнительной поддержки.

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с помощью официальной учетной записи Microsoft Azure для улучшения качества взаимодействия с [@AzureSupport](https://twitter.com/azuresupport)клиентами.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **получить поддержку**.
