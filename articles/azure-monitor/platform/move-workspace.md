---
title: Перемещение рабочей области Log Analytics в Azure Monitor | Документация Майкрософт
description: Узнайте, как переместить рабочую область Log Analytics в другую подписку или группу ресурсов.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093791"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Перемещение рабочей области Log Analytics в другую подписку или группу ресурсов

В этой статье вы узнаете, как переместить рабочую область Log Analytics в другую группу ресурсов или подписку в том же регионе. Дополнительные сведения о перемещении ресурсов Azure можно получить с помощью портал Azure, PowerShell, Azure CLI или REST API. [Переместите ресурсы в новую группу ресурсов или подписку](../../azure-resource-manager/resource-group-move-resources.md). 

> [!IMPORTANT]
> Невозможно переместить рабочую область в другой регион.

## <a name="verify-active-directory-tenant"></a>Проверка клиента Active Directory
Источник рабочей области и целевые подписки должны существовать в одном клиенте Azure Active Directory. Используйте Azure PowerShell, чтобы убедиться, что обе подписки имеют одинаковый идентификатор клиента.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>Удалить решения
Управляемые решения, установленные в рабочей области, будут перемещены с помощью Log Analytics операции перемещения рабочей области. Так как вы должны удалить связь из рабочей области с любой учетной записью службы автоматизации, необходимо удалить решения, основанные на этой ссылке.

Ниже перечислены решения, которые необходимо удалить. 

- Управление обновлениями
- Отслеживание изменений
- Запуск и остановка виртуальных машин в нерабочее время


### <a name="azure-portal"></a>портале Azure
Используйте следующую процедуру, чтобы удалить решения с помощью портал Azure:

1. Откройте меню для группы ресурсов, в которой установлены все решения.
2. Выберите решения для удаления.
3. Щелкните **удалить ресурсы** , а затем подтвердите удаление ресурсов, нажав кнопку **Удалить**.

![Удаление решений](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

Чтобы удалить решения с помощью PowerShell, используйте командлет [Remove-азресаурце](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) , как показано в следующем примере:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>Удаление правил генерации оповещений
Для решения " **Запуск и завершение виртуальных машин** " необходимо также удалить правила генерации оповещений, созданные решением. Используйте следующую процедуру в портал Azure, чтобы удалить эти правила.

1. Откройте меню **мониторинг** и выберите пункт **оповещения**.
2. Щелкните **Управление правилами оповещений**.
3. Выберите следующие три правила генерации оповещений, а затем нажмите кнопку **Удалить**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![удаление правил;](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Отменить связь с учетной записью автоматизации
Используйте следующую процедуру, чтобы отменить связь учетной записи службы автоматизации с рабочей областью с помощью портал Azure:

1. Откройте меню **учетные записи службы автоматизации** , а затем выберите учетную запись для удаления.
2. В разделе **связанные ресурсы** в меню выберите пункт **связанная Рабочая область**. 
3. Щелкните **Удалить связь рабочей области** , чтобы отменить связь рабочей области с учетной записью службы автоматизации.

    ![Удаление связи с рабочей областью](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Перемещение рабочей области

### <a name="azure-portal"></a>портале Azure
Используйте следующую процедуру для перемещения рабочей области с помощью портал Azure:

1. Откройте меню **log Analytics рабочие области** и выберите рабочую область.
2. На странице **Обзор** нажмите кнопку **изменить** рядом с **группой ресурсов** или **подпиской**.
3. Откроется новая страница со списком ресурсов, связанных с рабочей областью. Выберите ресурсы, которые нужно переместить в ту же целевую подписку и группу ресурсов, что и Рабочая область. 
4. Выберите целевую **подписку** и **группу ресурсов**. Если вы перемещаете рабочую область в другую группу ресурсов в той же подписке, вы не увидите параметр **Подписка** .
5. Нажмите кнопку **ОК** , чтобы переместить рабочую область и выбранные ресурсы.

    ![Портал](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Чтобы переместить рабочую область с помощью PowerShell, используйте [Move-азресаурце](/powershell/module/AzureRM.Resources/Move-AzureRmResource) , как показано в следующем примере:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> После операции перемещения удаленные решения и ссылки на учетную запись службы автоматизации необходимо перенастроить, чтобы восстановить предыдущее состояние рабочей области.


## <a name="next-steps"></a>Дополнительная информация
- Список ресурсов, которые поддерживают перемещение, см. в разделе [Поддержка операций перемещения для ресурсов](../../azure-resource-manager/move-support-resources.md).
