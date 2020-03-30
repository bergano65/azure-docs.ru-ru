---
title: Переместите учетную запись Azure Automation в другую подписку
description: В этой статье описывается, как переместить учетную запись автоматизации в другую подписку
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969826"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Переместите учетную запись Azure Automation в другую подписку

Azure предоставляет вам возможность перемещения некоторых ресурсов в новую группу ресурсов или подписку. Перемещать ресурсы можно через портал Azure, PowerShell, Azure CLI или aPI REST. Чтобы узнать больше о процессе, [см. Перемещение ресурсов в новую группу ресурсов или подписку.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

Учетные записи Azure Automation являются одним из ресурсов, которые могут быть перемещены. В этой статье вы узнаете шаги по перемещению учетных записей Automation на другой ресурс или подписку.

Высокоуровневые шаги по перемещению учетной записи Автоматизации:

1. Удалите свои решения.
2. Отсоедините рабочее пространство.
3. Перемещение учетной записи автоматизации.
4. Удалить и воссоздать учетные записи Run As.
5. Повторное включение решений.

## <a name="remove-solutions"></a>Удаление решений

Чтобы отсоединить рабочее пространство от учетной записи Автоматизации, эти решения должны быть удалены из рабочего пространства:
- **Отслеживание изменений и инвентаризация**
- **Управление обновлениями**
- **Запуск/остановка ВМ в нерабочее время**

В группе ресурсов найдите каждое решение и выберите **Delete**. На странице **Ресурсов удаления** подтвердите удаленные ресурсы и **выберите «Удалить».**

![Удаление решений с портала Azure](../media/move-account/delete-solutions.png)

Вы можете выполнить ту же задачу с помощью cmdlet [Remove-AzureRmResource,](/powershell/module/azurerm.resources/remove-azurermresource) как показано в следующем примере:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Дополнительные шаги для Start/Stop VMs

Для решения **Start/Stop VMs** необходимо также удалить правила оповещения, созданные решением.

На портале Azure перейдите в группу ресурсов и выберите > **правила оповещения**о > **мониторинге.** **Monitoring**

![Страница оповещения, показывающая выбор правил оповещения управления](../media/move-account/alert-rules.png)

На странице **Правил** следует увидеть список предупреждений, настроенных в этой группе ресурсов. Решение **Start/Stop VMs** создает три правила оповещения:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Выберите эти три правила оповещения, а затем выберите **Удалить**. Это действие удалит эти правила оповещения.

![Страница правил с просьбой подтвердить удаление для выбранных правил](../media/move-account/delete-rules.png)

> [!NOTE]
> Если на странице **Правил** не увидят никаких правил оповещения, измените **статус,** чтобы показать оповещения **инвалидов,** поскольку они могут быть отключены.

При удалении правил оповещения удалите группу действий, созданную для уведомлений решения **Start/Stop VMs.**

На портале Azure **Monitor** > выберите**группы действий** > Monitor Alerts**Manage.**

Выберите **StartStop_VM_Notification** из списка. На странице группы действий выберите **Удалить**.

![Страница группы действий, выберите удалить](../media/move-account/delete-action-group.png)

Аналогичным образом, вы можете удалить свою группу действий с помощью PowerShell с cmdlet [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) как видно из следующего примера:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Отсоедините рабочее пространство

На портале Azure выберите**рабочее пространство,** > **связанное с отделом автоматизации, связанное** > с ресурсами. **Automation account** Выберите **рабочее пространство Unlink,** чтобы отсоединить рабочее пространство от учетной записи Automation.

![Отсоединение рабочего пространства к учетной записи автоматизации](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Перемещение учетной записи автоматизации

После удаления предыдущих элементов можно продолжить удаление учетной записи Automation и ее runbooks. На портале Azure можно ознакомиться с группой ресурсов учетной записи Automation. Выберите **Переместить** > **Переместить в другую подписку**.

![Страница группы ресурсов, переход на другую подписку](../media/move-account/move-resources.png)

Выберите ресурсы в группе ресурсов, которые необходимо переместить. Убедитесь, что вы включаете свои **ресурсы автоматизации,** **Runbook**и **ресурсы рабочего пространства Log Analytics.**

После завершения переезда требуются дополнительные шаги, чтобы все работало.

## <a name="re-create-run-as-accounts"></a>Повторное создание учетных записей Run As

[Выполнить как учетные записи](../manage-runas-account.md) создать основную службу в active-каталоге Azure для проверки подлинности с помощью ресурсов Azure. При изменении подписки учетная запись Automation больше не использует существующую учетную запись Run As.

Перейдите на учетную запись Automation в новой подписке и выберите **Run в качестве учетных записей** в **настройках учетной записи.** Вы увидите, что учетные записи Run As теперь показаны как неполные.

![Выполнить Как счета являются неполными](../media/move-account/run-as-accounts.png)

Выберите каждый запуск как учетную запись. На странице **Свойств** выберите **Удалить,** чтобы удалить учетную запись Run As.

> [!NOTE]
> Если у вас нет разрешений на создание или просмотр учетных записей `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Run As, вы увидите следующее сообщение: Чтобы узнать о разрешениях, необходимых для настройки учетной записи Run As, см. [Разрешения, необходимые для настройки учетных записей Run As.](../manage-runas-account.md#permissions)

После удаления учетных записей после удаления учетных записей выберите **«Создать»** в **учетной записи Azure Run As.** На странице **Add Azure Run As As,** выберите **Создать** для создания run As учетной записи и основной службы. Повторите предыдущие шаги с **учетной записью Azure Classic Run As.**

## <a name="enable-solutions"></a>включение решений;

После повторного создания учетных записей Run As вы повторно запустите решения, которые были удалены до переезда. Чтобы включить **отслеживание изменений и управление запасами** и **обновлением,** выберите соответствующую возможность в учетной записи Автоматизации. Выберите рабочее пространство Log Analytics, перешедшее, и выберите **Enable.**

![Повторное включение решений в перемещенной учетной записи автоматизации](../media/move-account/reenable-solutions.png)

Машины, которые находятся на борту с вашими решениями, будут видны при подключении существующего рабочего пространства Log Analytics.

Чтобы включить **Start/Stop VMs** в нерабочее время, необходимо передислоцировать решение. В **рамках «Связанных ресурсов»** выберите **Start/Stop VMs** > Подробнее о создании**и возможности** > **создания** решения для запуска развертывания.

На странице **добавления решения** выберите учетную запись Log Analytics Workspace и Automation.

![Добавить меню решения](../media/move-account/add-solution-vm.png)

Подробные инструкции по настройке решения можно найти [в azure Automation.](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Проверка после переезда

Когда перемещение будет завершено, проверьте следующий список задач, которые должны быть проверены:

|Функция|Тесты|Ссылка на устранение неполадок|
|---|---|---|
|Модули runbook|Запуск может успешно работать и подключаться к ресурсам Azure.|[Troubleshoot runbooks](../troubleshoot/runbooks.md)
|Система управления версиями|Можно запустить ручную синхронизацию репо управления исходным управлением.|[Интеграция управления исходным источником](../source-control-integration.md)|
|Отслеживание изменений и инвентаризации|Убедитесь, что вы видите текущие данные инвентаризации с ваших машин.|[Отслеживание изменений неполадок](../troubleshoot/change-tracking.md)|
|Управление обновлениями|Убедитесь, что вы видите ваши машины, и они здоровы.</br>Запустите развертывание обновления тестового программного обеспечения.|[Управление обновлением Troubleshoot](../troubleshoot/update-management.md)|
|Общие ресурсы|Убедитесь, что вы видите все ваши общие ресурсы, такие как [учетные данные,](../shared-resources/credentials.md) [переменные](../shared-resources/variables.md)и т.д.|

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о перемещении ресурсов в Azure можно узнать [в Azure, читайте в](../../azure-resource-manager/management/move-support-resources.md)отделе ресурсов Перемещение в Azure.
