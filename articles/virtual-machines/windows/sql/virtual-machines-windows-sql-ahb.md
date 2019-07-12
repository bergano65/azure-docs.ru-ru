---
title: Как изменить модель лицензирования для виртуальной Машины SQL Server в Azure
description: Узнайте, как переключиться лицензирования для виртуальной машины SQL в Azure из «Оплата по мере использования» в «перевести your собственной лицензии» Преимущество гибридного использования Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786760"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Как изменить модель лицензирования для виртуальной машины SQL Server в Azure
В этой статье описывается процесс, позволяющий изменить модель лицензирования для виртуальной машины SQL Server в Azure с помощью нового поставщика ресурсов виртуальной машины SQL **Microsoft.SqlVirtualMachine**.

Существует две модели лицензирования для виртуальной машины (VM) для размещения SQL Server — Оплата по мере использования и протокола преимущество (гибридного использования AZURE, преимущество использования гибридного Azure). И теперь, с помощью портала Azure, Azure CLI или PowerShell можно изменить модель лицензирования виртуальной Машине SQL Server. 

**Оплаты по мере использования** модели (PAYG) означает, что стоимость секунды выполнения виртуальной Машины Azure включает в себя стоимость лицензии SQL Server.
[Преимущество (гибридного использования AZURE — Преимущество гибридного на Azure)](https://azure.microsoft.com/pricing/hybrid-benefit/) позволяет использовать собственную лицензию SQL Server с помощью виртуальной Машины под управлением SQL Server. 

Microsoft Преимущество гибридного использования Azure для SQL Server позволяет, использовать лицензии SQL Server с Software Assurance («полное лицензия») на виртуальных машинах Azure. Благодаря преимуществу гибридного использования Azure для SQL Server клиенты не будете оплачивать использование лицензии SQL Server на виртуальной Машине, но они должны по-прежнему оплачивать затраты на базовые ресурсы вычисления облако (то есть базовый тариф), хранилище и резервное копирование, а также операций ввода-вывода, связанных с их u SE служб (если применимо).

В соответствии с условиях для продуктов Microsoft «клиентов необходимо указать, что они используют база данных SQL Azure (управляемый экземпляр эластичного пула и отдельной базы данных), фабрика данных Azure, SQL Server Integration Services или виртуальные машины SQL Server в разделе гибридного Преимущество для SQL Server при настройке рабочих нагрузок в Azure».

Чтобы указать использование преимуществ гибридного использования Azure для SQL Server на виртуальной Машине Azure и соответствовать требованиям, можно тремя способами:

1. Подготовьте виртуальную машину, используя образ BYOL SQL Server в Azure Marketplace, доступен только для клиентов с соглашением Enterprise.
1. Подготовка виртуальной машины с помощью образа PAYG SQL Server из Azure marketplace и активируйте Преимущество гибридного использования AZURE.
1. Самостоятельную установку SQL Server на виртуальной Машине Azure вручную [зарегистрировать их виртуальной Машине SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) и активировать преимущество гибридного использования AZURE.

Тип лицензии SQL Server устанавливается в том случае, когда виртуальная машина подготавливается и можно изменить в любое время позже. Переключение между моделями лицензирования влечет за собой **без простоя**, не перезапускает виртуальную Машину, добавляет **без дополнительных затрат** (на самом деле, активировав Преимущество гибридного использования AZURE *уменьшает* затрат) и является **вступает в силу немедленно**. 

## <a name="prerequisites"></a>Предварительные требования

Для использования поставщика ресурсов виртуальной машины SQL требуется расширение SQL IaaS. Таким образом, чтобы продолжить использование поставщика ресурсов виртуальной машины SQL, вам потребуется следующее:
- [Подписка Azure](https://azure.microsoft.com/free/).
- [Программы Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Объект [виртуальной Машины SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) зарегистрировано [поставщика ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md) установлен. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Изменение лицензии на виртуальные машины, уже зарегистрирована в поставщике ресурсов 

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Вы можете изменить модель лицензирования непосредственно с портала. 

1. Откройте [портала Azure](https://portal.azure.com) и запустите [ресурсов виртуальных машин SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) для виртуальной Машины SQL Server. 
1. Выберите **Настройка** под **параметры**. 
1. Выберите **Преимущество гибридного использования Azure** параметр и установите флажок, чтобы подтвердить наличие лицензии SQL Server с Software Assurance. 
1. Выберите **применить** в нижней части **Настройка** страницы. 

![Преимущество гибридного использования AZURE на портале](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ ИНТЕРФЕЙСА КОМАНДНОЙ СТРОКИ](#tab/bash)

Вы можете изменить модель лицензирования с помощью Azure CLI.  

В следующем фрагменте кода для переключения модели оплаты по мере использования лицензии BYOL (и преимущество гибридного использования Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

В следующем фрагменте кода переключается использовать владельцем лицензии модели оплаты по мере использования: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Вы можете изменить модель лицензирования с помощью PowerShell.

В следующем фрагменте кода для переключения модели оплаты по мере использования лицензии BYOL (и преимущество гибридного использования Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

В следующем фрагменте кода модели BYOL переключается на оплату по мере использования:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Изменении лицензии для виртуальных машин, не зарегистрирована в поставщике ресурсов

Если вы подготовили виртуальную Машину SQL Server на основе образов Azure Marketplace (PAYG) тип лицензии SQL будет (PAYG). Если вы подготовили виртуальную Машину SQL Server, используя образ BYOL в Azure Marketplace тип лицензии будет AHUB. Все виртуальные машины SQL Server подготовленные по умолчанию (PAYG) или образы BYOL Azure Marketplace будет автоматически зарегистрирован с поставщиком ресурсов виртуальной Машины SQL, чтобы они могли изменять [тип лицензии](#change-license-for-vms-already-registered-with-resource-provider)

Вам доступно только для локальной установки SQL Server на виртуальной Машине Azure с помощью преимуществ гибридного использования Azure и вы должны [зарегистрировать эти виртуальные машины с поставщиком ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , задав как Преимущество гибридного использования AZURE, чтобы указать использование Преимущество гибридного использования AZURE согласно лицензии SQL Server Условиях для продуктов Microsoft.

Тип лицензии виртуальную Машину SQL Server (PAYG) или Преимущество гибридного использования AZURE можно изменить только в случае, если виртуальная машина SQL регистрируется с поставщиком ресурсов виртуальной Машины SQL; и все виртуальные машины SQL должен быть зарегистрирован в SQL RP виртуальной Машины для обеспечения соответствия лицензиям.

## <a name="remarks"></a>Примечания

 - Клиенты Azure Cloud Solution Partner (CSP) можно использовать преимущество гибридного использования Azure, сначала развернув оплаты по мере использования виртуальной Машины и затем преобразуется использовать владельцем лицензии при наличии действующего соглашения SA.
 - Если удалить ресурс виртуальной Машины SQL Server, будет выполнен переход обратно параметр жестко лицензии изображения. 
  - Возможность изменить модель лицензирования входит в состав поставщика ресурсов виртуальной Машины SQL. Развертывание образа marketplace на портале Azure автоматически регистрирует виртуальную Машину SQL Server с поставщиком ресурсов. Тем не менее клиенты, которые самостоятельно при установке SQL Server потребуется вручную [зарегистрировать их виртуальной Машине SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Добавление виртуальной Машины SQL Server в группу доступности требует повторного создания ВМ. Как такие, все виртуальные машины, добавление в доступности набора вернусь к типу лицензии с оплатой по мере использования по умолчанию и потребуется снова включить Преимущество гибридного использования AZURE. 


## <a name="limitations"></a>Ограничения

 - Изменение модели лицензирования доступен только клиентам с подпиской software assurance.
 - Изменение модели лицензирования поддерживается только для выпуска standard и enterprise SQL Server. Изменений в лицензии для Express и веб-разработчиков не поддерживаются. 
 - Изменение модели лицензирования поддерживается только для виртуальных машин, развернутых с помощью модели Resource Manager. Виртуальные машины, развернутые с помощью классической модели не поддерживаются. 
 - Изменение модели лицензирования доступна только для установок общедоступного облака.
 - Изменение модели лицензирования, поддерживается только для виртуальных машин с одним сетевым Адаптером (сетевой интерфейс). На виртуальных машинах, имеющих более одной сетевой КАРТЫ, следует сначала удалить один из сетевых адаптеров (с помощью портала Azure) перед выполнением процедуры. В противном случае возникнет ошибка следующего вида: `The virtual machine '\<vmname\>' has more than one NIC associated.` Несмотря на то, что можно добавить сетевой Адаптер на виртуальную машину, после изменения режима лицензирования, операции, выполняемые на странице конфигурации SQL на портале Azure, такие как автоматическое исправление и резервное копирование, больше не будет считаться поддерживается.


## <a name="known-errors"></a>Известные ошибки

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Ресурс "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<группы ресурсов >" в группе ресурсов "\<группы ресурсов >" не найден. Не удалось найти свойство «sqlServerLicenseType» для этого объекта. Убедитесь, что свойство существует и можно задать.
Эта ошибка возникает при попытке изменить модель лицензирования на виртуальную Машину SQL Server, который не был зарегистрирован с поставщиком ресурсов виртуальной Машины SQL. Вам потребуется зарегистрировать поставщик ресурсов должен вашей [подписки](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), а затем зарегистрировать виртуальную Машину SQL Server в SQL [поставщика ресурсов](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Не удается проверить аргумент в параметре "Sku"
Эта ошибка возникает при попытке изменить модель лицензирования виртуальной Машины SQL Server при использовании Azure PowerShell > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Чтобы устранить эту ошибку, раскомментируйте эти строки в приведенном выше фрагменте кода PowerShell при изменении модели лицензирования:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Чтобы проверить версию Azure PowerShell, используйте следующий код:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Вопросы и ответы по SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-faq.md).
* [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-release-notes.md).


