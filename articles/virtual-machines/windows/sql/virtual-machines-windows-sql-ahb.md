---
title: Изменение модели лицензирования для SQL Server виртуальной машины в Azure
description: Узнайте, как переключить лицензирование для SQL Server виртуальной машины в Azure с оплатой по мере использования на собственную лицензию с помощью Преимущество гибридного использования Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 00262b48b8fa2fd1292554155e8ec8e933d886e6
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690910"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Изменение модели лицензирования для SQL Server виртуальной машины в Azure
В этой статье описывается, как изменить модель лицензии для SQL Server виртуальной машины в Azure с помощью нового поставщика ресурсов виртуальной машины SQL ( **Microsoft. склвиртуалмачине**).

Существует две модели лицензирования для виртуальной машины, на которой размещена SQL Server: оплата по мере использования и Преимущество гибридного использования Azure. Вы можете изменить модель лицензии SQL Server виртуальной машины с помощью портал Azure, Azure CLI или PowerShell. 

Модель с оплатой по мере использования означает, что стоимость использования виртуальной машины Azure в секунду включает стоимость лицензии на SQL Server.
[Преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) позволяет использовать собственную лицензию SQL Server с виртуальной машиной, на которой выполняется SQL Server. 

Преимущество гибридного использования Azure позволяет использовать лицензии SQL Server с Software Assurance ("квалифицированная лицензия") на виртуальных машинах Azure. С Преимущество гибридного использования Azure клиенты не наставляются с оплатой за использование лицензии на SQL Server на виртуальной машине. Но они по-прежнему должны платить за стоимость базового облачного вычислений (то есть базового тарифа), хранилища и резервных копий. Кроме того, они должны платить за ввод-вывод, связанный с использованием служб (как применимо).

В соответствии с условиями продукта Майкрософт: "клиенты должны указать, что они используют базу данных SQL Azure (Управляемый экземпляр, Эластичный пул и отдельная база данных), фабрику данных Azure, SQL Server Integration Services или виртуальные машины SQL Server в Azure. Преимущество гибридного использования для SQL Server при настройке рабочих нагрузок в Azure ".

Чтобы указать использование Преимущество гибридного использования Azure для SQL Server на виртуальной машине Azure и обеспечить соответствие, у вас есть три варианта:

- Подведите виртуальную машину с помощью собственного образа SQL Server с лицензией из Azure Marketplace. Этот параметр доступен только для клиентов, у которых есть Соглашение Enterprise.
- Подготавливаете виртуальную машину с помощью образа SQL Server с оплатой по мере использования из Azure Marketplace и активируйте Преимущество гибридного использования Azure.
- Самостоятельная установка SQL Server на виртуальной машине Azure, ручная [Регистрация в поставщике ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md)и активация преимущество гибридного использования Azure.

Тип лицензии SQL Server задается при подготовке виртуальной машины. Его можно изменить в любое время. Переключение между моделями лицензий не приводит к простою, не перезапускает виртуальную машину или службу SQL Server, не добавляет никаких дополнительных затрат и вступает в силу немедленно. На самом деле, активация Преимущество гибридного использования Azure *сокращает* затраты.

## <a name="prerequisites"></a>Технические условия

Изменение модели лицензирования SQL Server виртуальной машины имеет следующие требования. 

- [Подписка Azure](https://azure.microsoft.com/free/).
- [SQL Server виртуальную машину](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) , зарегистрированную в [ПОСТАВЩИКЕ ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) является обязательным требованием для использования [преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Изменение лицензии для виртуальных машин, уже зарегистрированных в поставщике ресурсов 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Модель лицензии можно изменить непосредственно на портале: 

1. Откройте [портал Azure](https://portal.azure.com) и откройте ресурс " [виртуальные машины SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) " для SQL Server виртуальной машины. 
1. Выберите **Настройка** в разделе **Параметры**. 
1. Выберите параметр **преимущество гибридного использования Azure** и установите флажок, чтобы подтвердить наличие лицензии на SQL Server с программой Software Assurance. 
1. В нижней части страницы **Настройка** нажмите кнопку **Применить** . 

![Преимущество гибридного использования Azure на портале](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Для изменения модели лицензии можно использовать Azure CLI.  

Следующий фрагмент кода переключает модель лицензии с оплатой по мере использования на собственную лицензию (или с помощью Преимущество гибридного использования Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

В следующем фрагменте кода переключается модель лицензирования с оплатой по мере использования: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Для изменения модели лицензирования можно использовать PowerShell.

Следующий фрагмент кода переключает модель лицензии с оплатой по мере использования на собственную лицензию (или с помощью Преимущество гибридного использования Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

В следующем фрагменте кода переключается модель лицензирования с оплатой по мере использования:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Изменение лицензии для виртуальных машин, не зарегистрированных в поставщике ресурсов

Если вы подготовили SQL Serverную виртуальную машину с помощью образов Azure Marketplace с оплатой по мере использования, тип лицензии SQL Server будет иметь вид "Оплата по мере". Если вы подготовили SQL Serverную виртуальную машину с помощью собственного образа лицензии из Azure Marketplace, тип лицензии будет AHUB. Все SQL Server виртуальные машины, подготовленные по умолчанию (с оплатой по мере использования) или собственные образы Azure Marketplace с собственной лицензией, будут автоматически зарегистрированы в поставщике ресурсов виртуальной машины SQL, чтобы они могли изменить [тип лицензии](#change-the-license-for-vms-already-registered-with-the-resource-provider).

Вы можете самостоятельно установить SQL Server на виртуальной машине Azure с помощью Преимущество гибридного использования Azure. Необходимо [зарегистрировать эти виртуальные машины с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , установив для лицензии SQL Server значение преимущество гибридного использования Azure, чтобы указать преимущество гибридного использования Azureное использование в соответствии с условиями продукта Майкрософт.

Тип лицензии SQL Server виртуальной машины можно изменить как "Оплата по мере использования" или "Преимущество гибридного использования Azure только в том случае, если SQL Server виртуальная машина зарегистрирована в поставщике ресурсов виртуальной машины SQL.

## <a name="remarks"></a>Remarks

- Клиенты поставщика облачных решений Azure (CSP) могут использовать Преимущество гибридного использования Azure, сначала Развертывая виртуальную машину с оплатой по мере использования, а затем преобразуя ее в собственную лицензию, если у них есть активная программа Software Assurance.
- Если удалить ресурс виртуальной машины SQL Server, вы вернетесь к жестко запрограммированному параметру лицензии образа. 
- Возможность изменения модели лицензии является функцией поставщика ресурсов виртуальной машины SQL. Развертывание образа Azure Marketplace с помощью портал Azure автоматически регистрирует SQL Server виртуальную машину с помощью поставщика ресурсов. Но клиенты, которые самостоятельно устанавливают SQL Server, должны вручную [зарегистрировать свои виртуальные машины SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Для добавления SQL Server виртуальной машины в группу доступности необходимо повторно создать виртуальную машину. Таким образом, все виртуальные машины, добавленные в группу доступности, будут возвращаться к типу лицензии с оплатой по мере использования по умолчанию. Преимущество гибридного использования Azure потребуется снова включить. 


## <a name="limitations"></a>Ограничения

Изменение модели лицензии:
   - Доступно только для клиентов с [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Поддерживается только для выпусков Standard и Enterprise SQL Server. Изменения лицензий для Express, Web и Developer не поддерживаются. 
   - Поддерживается только для виртуальных машин, развернутых с помощью модели Azure Resource Manager. Виртуальные машины, развернутые с помощью классической модели, не поддерживаются. 
   - Доступно только для облаков общедоступного облака или Azure для государственных организаций. 
   - Поддерживается только на виртуальных машинах с одним сетевым интерфейсом (NIC). 


## <a name="known-errors"></a>Известные ошибки

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Ресурс "Microsoft. Склвиртуалмачине/Склвиртуалмачинес/\<Resource-Group >" в группе ресурсов "\<ресурс-группа >" не найден.

Эта ошибка возникает при попытке изменить модель лицензии на SQL Server виртуальной машине, которая не зарегистрирована в поставщике ресурсов виртуальной машины SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Вам потребуется зарегистрировать подписку в поставщике ресурсов, а затем [зарегистрировать SQL Server виртуальную машину с помощью поставщика ресурсов](virtual-machines-windows-sql-register-with-resource-provider.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>С виртуальной машиной "\<vmname\>" связано более одной сетевой карты.

Эта ошибка возникает на виртуальных машинах, имеющих более одного сетевого адаптера. Удалите одну из сетевых карт перед изменением модели лицензирования. Хотя сетевую карту можно добавить обратно в виртуальную машину после изменения модели лицензирования, операции в портал Azure, такие как автоматическое резервное копирование и установка исправлений, больше не будут поддерживаться. 


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Часто задаваемые вопросы о SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценам для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


