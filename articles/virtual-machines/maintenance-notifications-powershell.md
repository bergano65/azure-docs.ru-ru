---
title: Получайте уведомления об обслуживании для VMs Azure с помощью PowerShell
description: Просматривайте уведомления об обслуживании виртуальных машин, работающих в Azure, и приступайте к обслуживанию самообслуживания с помощью PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916088"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Обработка планового технического обслуживания с помощью PowerShell

**Эта статья распространяется на виртуальные машины под управлением Как Linux, так и Windows.**

Вы можете использовать Azure PowerShell, чтобы увидеть, когда вывешитесь в [эксплуатации.](maintenance-notifications.md) Информацию о плановом обслуживании можно получить с помощью командлета [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm), используя параметр `-status`.
  
Сведения об обслуживании возвращаются, только если имеется запланированное обслуживание. Если нет запланированного обслуживания, влияющего на виртуальную машину, командлет не возвращает информацию об обслуживании. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

В разделе MaintenanceRedeployStatus возвращаются следующие свойства: 

| Значение | Описание   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Указывает, можно ли сейчас запустить обслуживание на виртуальной машине |
| PreMaintenanceWindowStartTime         | Начало периода самообслуживания, когда можно инициировать обслуживание на виртуальной машине |
| PreMaintenanceWindowEndTime           | Завершение периода самообслуживания, когда можно инициировать обслуживание на виртуальной машине |
| MaintenanceWindowStartTime            | Начало периода запланированного обслуживания, в течение которого Azure запускает обслуживание для виртуальной машины. |
| MaintenanceWindowEndTime              | Завершение периода запланированного обслуживания, в течение которого Azure запускает обслуживание для виртуальной машины. |
| LastOperationResultCode               | Результат последней попытки инициирования обслуживания на виртуальной машине |



Вы также можете получить информацию о состоянии обслуживания всех виртуальных машин в группе ресурсов с помощью командлета [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm), не указывая виртуальную машину.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

Следующий пример PowerShell берет ваш идентификатор подписки и возвращает список вс-м вс-услуг, которые запланированы на техническое обслуживание.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Запуск обслуживания вашей виртуальной машины с помощью PowerShell

Используя информацию из функции в предыдущем разделе, следующая команда запускает обслуживание на виртуальной машине, если для свойства **IsCustomerInitiatedMaintenanceAllowed** задано значение true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Классические развертывания

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Если у вас все еще есть устаревшие виртуальные машины, развернутые с использованием классической модели развертывания, вы можете сделать запрос к ним и запустить их обслуживание с помощью PowerShell.

Чтобы узнать состояние обслуживания виртуальной машины, введите:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Чтобы начать обслуживание классической виртуальной машины, введите:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете обрабатывать запланированное техническое обслуживание с помощью [Azure CLI](maintenance-notifications-cli.md) или [портала.](maintenance-notifications-portal.md)
