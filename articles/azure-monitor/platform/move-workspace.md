---
title: Перемещение рабочего пространства аналитики журналов в Azure Monitor (ru) Документы Майкрософт
description: Узнайте, как переместить рабочее пространство Log Analytics в другую группу подписчиков или ресурсов.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659498"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Перемещение рабочего пространства анализа журналов в другую группу по подписке или ресурсам

В этой статье вы узнаете шаги по перемещению рабочего пространства Log Analytics в другую группу ресурсов или подписку в том же регионе. Вы можете узнать больше о перемещении ресурсов Azure через портал Azure, PowerShell, Azure CLI или aPI REST. [перемещение ресурсов в новую группу ресурсов или подписку.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) 

> [!IMPORTANT]
> Нельзя перемещать рабочее пространство в другой регион.

## <a name="verify-active-directory-tenant"></a>Проверка арендатора Active Directory
Источник рабочего пространства и подписки назначения должны существовать в пределах одного и того же арендатора Azure Active Directory. Используйте Azure PowerShell, чтобы убедиться, что обе подписки имеют один и тот же идентификатор клиента.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Соображения перемещения рабочего пространства
Управляемые решения, установленные в рабочей области, будут перемещены с помощью операции перемещения рабочего пространства Log Analytics. Подключенные агенты будут оставаться подключенными и хранить отправку данных в рабочее пространство после перемещения. Поскольку операция перемещения требует, чтобы не было никакой связи из рабочего пространства с любой учетной записью автоматизации, решения, которые полагаются на эту ссылку, должны быть удалены.

Решения, которые должны быть удалены, прежде чем вы сможете развязать учетную запись автоматизации:

- Управление обновлениями
- Отслеживание изменений
- Запуск и остановка виртуальных машин в нерабочее время


### <a name="delete-in-azure-portal"></a>Удаление на портале Azure
Используйте следующую процедуру для удаления решений с помощью портала Azure:

1. Откройте меню для группы ресурсов, в которую установлены любые решения.
2. Выберите решения для удаления.
3. Нажмите **Удалить ресурсы,** а затем подтвердить ресурсы, которые будут удалены, нажав **Удалить**.

![Удаление решений](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Удалить с помощью PowerShell

Чтобы удалить решения с помощью PowerShell, используйте cmdlet [Remove-AzResource,](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) как показано в следующем примере:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Удалить правила оповещения
Для решения **Start/Stop VMs** необходимо также удалить правила оповещения, созданные решением. Для устранения этих правил используйте следующую процедуру на портале Azure.

1. Откройте меню **Монитор,** а затем выберите **оповещения.**
2. Нажмите **Правила оповещения Управления.**
3. Выберите следующие три правила оповещения, а затем нажмите **Удалить**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![удаление правил;](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Учетная запись автоматизации ссылки
Используйте следующую процедуру для развязывание учетной записи Automation из рабочего пространства с помощью портала Azure:

1. Откройте меню **учетных записей автоматизации,** а затем выберите учетную запись для удаления.
2. В разделе **«Связанные ресурсы»** меню выберите **рабочее пространство Linked**. 
3. Нажмите **Unlink рабочее пространство,** чтобы отвязать рабочее пространство от вашей учетной записи автоматизации.

    ![Удаление связи с рабочей областью](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Перемещение рабочего пространства

### <a name="azure-portal"></a>Портал Azure
Используйте следующую процедуру для перемещения рабочего пространства с помощью портала Azure:

1. Откройте меню **рабочих областей анализа журналов,** а затем выберите рабочее пространство.
2. На странице **«Обзор»** нажмите **кнопку «Изменение»** рядом с **группой ресурсов** или **подпиской.**
3. Новая страница открывается со списком ресурсов, связанных с рабочим пространством. Выберите ресурсы для перемещения в ту же подписку назначения и группу ресурсов, что и рабочее пространство. 
4. Выберите **группу** **подписки** и ресурсов назначения. Если вы перемещаете рабочее пространство в другую группу ресурсов в той же подписке, вы не увидите опцию **подписки.**
5. Нажмите **OK,** чтобы переместить рабочее пространство и выбранные ресурсы.

    ![Портал](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Для перемещения рабочего пространства с помощью PowerShell используйте [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) в следующем примере:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> После операции перемещения удаленные решения и ссылка на учетную запись Automation должны быть перенастроены, чтобы вернуть рабочее пространство в прежнее состояние.


## <a name="next-steps"></a>Дальнейшие действия
- Список ресурсов, поддерживающих перемещение [Move operation support for resources](../../azure-resource-manager/management/move-support-resources.md)ресурсов, см.
