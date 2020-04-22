---
title: Переместите учетную запись Azure Automation в другую подписку
description: В этой статье описывается, как переместить учетную запись Автоматизации в другую подписку.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681892"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Переместите учетную запись Azure Automation в другую подписку

Azure Automation позволяет переместить некоторые ресурсы в новую группу ресурсов или подписку. Перемещать ресурсы можно через портал Azure, PowerShell, Azure CLI или aPI REST. Чтобы узнать больше о процессе, [см. Перемещение ресурсов в новую группу ресурсов или подписку.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

Учетная запись Azure Automation — это один из ресурсов, которые можно перемещать. В этой статье вы научитесь перемещать учетные записи автоматизации на другой ресурс или подписку. Высокоуровневые шаги для перемещения учетной записи Автоматизации:

1. Удалите свои решения.
2. Отсоедините рабочее пространство.
3. Перемещение учетной записи автоматизации.
4. Удалить и воссоздать учетные записи Run As.
5. Повторное включение решений.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="remove-solutions"></a>Удаление решений

Чтобы отсоединить рабочее пространство от учетной записи Automation, необходимо удалить эти решения из рабочего пространства:

- Отслеживание изменений и инвентаризация
- Управление обновлениями
- Запуск/остановка ВМ в нерабочее время

1. Найдите группу ресурсов на портале Azure.
2. Найдите каждое решение и нажмите **«Удалить»** на странице Ресурсов удаления.

    ![Удаление решений с портала Azure](../media/move-account/delete-solutions.png)

    Если вы предпочитаете, вы можете удалить решения с помощью cmdlet [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Удалить правила оповещения для решения Start/Stop VMs в нерабочее время

Для решения Start/Stop VMs в нерабочее время необходимо также удалить правила оповещения, созданные решением.

1. На портале Azure перейдите в группу ресурсов и выберите > **правила оповещения**о > **мониторинге.** **Monitoring**

![Страница оповещения, показывающая выбор правил оповещения управления](../media/move-account/alert-rules.png)

2. На странице Правил следует увидеть список предупреждений, настроенных в этой группе ресурсов. Решение создает следующие правила:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Выберите правила по одному и нажмите **Удалить,** чтобы удалить их.

    ![Страница правил с просьбой подтвердить удаление для выбранных правил](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Если на странице Правил не увидят никаких правил оповещения, измените поле **состояния** на отключенное, чтобы показать оповещения инвалидов, поскольку вы могли отключить их.

4. При удалении правил оповещения необходимо удалить группу действий, созданную для уведомлений о решениях Start/Stop vMs в нерабочее время. На портале Azure **Monitor** > выберите**группы действий** > Monitor Alerts**Manage.**

5. Выберите **StartStop_VM_Notification**. 

6. На странице группы действий выберите **Удалить**.

    ![Страница группы действий](../media/move-account/delete-action-group.png)

    Если вы предпочитаете, вы можете удалить свою группу действий с помощью cmdlet [Remove-AzActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Отсоедините рабочее пространство

Теперь вы можете отсоединить рабочее пространство:

1. На портале Azure выберите**рабочее пространство,** > **связанное с отделом автоматизации, связанное** > с ресурсами. **Automation account** 

2. Выберите **рабочее пространство Unlink,** чтобы отсоединить рабочее пространство от учетной записи Automation.

    ![Отсоединение рабочего пространства к учетной записи автоматизации](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Перемещение учетной записи автоматизации

Теперь вы можете переместить учетную запись Автоматизации и ее runbooks. 

1. На портале Azure можно ознакомиться с группой ресурсов учетной записи Automation. Выберите **Переместить** > **Переместить в другую подписку**.

    ![Страница группы ресурсов, переход на другую подписку](../media/move-account/move-resources.png)

2. Выберите ресурсы в группе ресурсов, которые необходимо переместить. Убедитесь, что вы включаете свои учетные записи автоматизации, журналы и ресурсы рабочего пространства Log Analytics.

## <a name="recreate-run-as-accounts"></a>Воссоздание run As учетных записей

[Выполнить как учетные записи](../manage-runas-account.md) создать основную службу в active-каталоге Azure для проверки подлинности с помощью ресурсов Azure. При изменении подписки учетная запись Automation больше не использует существующую учетную запись Run As. Чтобы воссоздать учетные записи Run As:

1. Перейдите на учетную запись Automation в новой подписке и выберите **Run в качестве учетных записей** в **настройках учетной записи.** Вы увидите, что учетные записи Run As теперь показаны как неполные.

    ![Выполнить Как счета являются неполными](../media/move-account/run-as-accounts.png)

2. Удалите учетные записи Run As по одному с помощью кнопки **«Удалить»** на странице Свойств. 

    > [!NOTE]
    > Если у вас нет разрешений на создание или просмотр учетных записей Run As, вы увидите следующее сообщение: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Чтобы узнать о разрешениях, необходимых для настройки учетной записи Run As, см. [Разрешения, необходимые для настройки учетных записей Run As.](../manage-runas-account.md#permissions)

3. После удаления учетных записей Run As выберите **«Создать»** в **учетной записи Azure Run As.** 

4. На странице Add Azure Run As As, выберите **Создать** для создания run As учетной записи и основной службы. 

5. Повторите приведенные выше шаги с помощью учетной записи Azure Classic Run As.

## <a name="enable-solutions"></a>включение решений;

После воссоздания учетных записей Run As необходимо повторно включить решения, которые были удалены перед перемещением: 

1. Чтобы включить решение отслеживания и инвентаризации изменений, выберите отслеживание изменений и инвентаризацию в учетной записи Автоматизации. Выберите рабочее пространство Log Analytics, которое переместило, и выберите **Enable.**

2. Повторите шаг 1 для решения управления обновлением.

    ![Повторное включение решений в перемещенной учетной записи автоматизации](../media/move-account/reenable-solutions.png)

3. Машины, которые находятся на борту с вашими решениями, видны при подключении существующего рабочего пространства Log Analytics. Чтобы включить решение Start/Stop VMs в нерабочее время, необходимо передислоцировать решение. В **рамках «Связанных ресурсов»** выберите **Start/Stop VMs** > Подробнее о создании**и возможности** > **создания** решения для запуска развертывания.

4. На странице добавления решения выберите рабочую область и учетную запись «Автоматизация журнала».

    ![Добавить меню решения](../media/move-account/add-solution-vm.png)

5. Найдите решение, описанное в [Start/Stop VMs в нерабочее время в Azure Automation.](../automation-solution-vm-management.md)

## <a name="verify-the-move"></a>Проверить ход

Когда перемещение будет завершено, убедитесь, что возможности, перечисленные ниже, включены. 

|Функция|Тесты|Устранение неполадок|
|---|---|---|
|Модули runbook|Запуск может успешно работать и подключаться к ресурсам Azure.|[Troubleshoot runbooks](../troubleshoot/runbooks.md)
|Система управления версиями|Можно запустить ручную синхронизацию в репозитории управления исходным элементом.|[Интеграция системы управления версиями](../source-control-integration.md)|
|Отслеживание изменений и инвентаризации|Убедитесь, что вы видите текущие данные инвентаризации с ваших машин.|[Отслеживание изменений неполадок](../troubleshoot/change-tracking.md)|
|Управление обновлениями|Убедитесь, что вы видите ваши машины и что они здоровы.</br>Запустите развертывание обновления тестового программного обеспечения.|[Управление обновлением Troubleshoot](../troubleshoot/update-management.md)|
|Общие ресурсы|Убедитесь, что вы видите все общие ресурсы, такие как [учетные данные,](../shared-resources/credentials.md) [переменные](../shared-resources/variables.md)и тому подобное.|

## <a name="next-steps"></a>Следующие шаги

Подробнее о перемещении ресурсов в Azure можно узнать [в Azure, читайте в](../../azure-resource-manager/management/move-support-resources.md)отделе ресурсов Перемещение в Azure.
