---
title: Измените модель лицензии для VM сервера S'L в Azure
description: Узнайте, как переключить лицензирование для виртуальной машины сервера S'L В Azure с оплаты по мере использования собственной лицензии с помощью Azure Hybrid Benefit.
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
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201834"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Измените модель лицензии для виртуальной машины сервера S'L в Azure
В этой статье описывается, как изменить модель лицензии для виртуальной машины S'L Server (VM) в Azure с помощью нового поставщика ресурсов ВМ, **Microsoft.SqlVirtualMachine**.

Существует три модели лицензий для VM, которая размещает сервер S'L: оплата по мере вашего узла, гибридная выгода Azure и аварийное восстановление (DR). Вы можете изменить модель лицензии вашего S'L Server VM с помощью портала Azure, Azure CLI или PowerShell. 

- Модель **с оплатой по мере работы** означает, что стоимость в секунду работы Azure VM включает стоимость лицензии S'L Server.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) позволяет использовать собственную лицензию S'L Server с помощью VM, который работает под управлением сервера S'L. 
- Тип лицензии **аварийного восстановления** используется для [бесплатной реплики DR](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) в Azure. 

Azure Hybrid Benefit позволяет использовать лицензии S'L Server с software Assurance («Квалифицированная лицензия») на виртуальных машинах Azure. С Azure Hybrid Benefit клиенты не взимаются за использование лицензии S'L Server на VM. Но они все равно должны оплачивать стоимость базовых облачных вычислений (т.е. базовой ставки), хранения и резервного копирования. Они также должны оплачивать вв./о, связанные с их использованием услуг (при необходимости).

В соответствии с условиями продукта Майкрософт: «Клиенты должны указать, что они используют базу данных Azure S'L (управляемая инстанция, эластичный пул и единая база данных), фабрику данных Azure, службы интеграции серверов S'L или виртуальные машины сервера S'L под Azure Гибридная выгода для сервера S'L при настройке рабочих нагрузок на Azure."

Чтобы указать на использование гибридной выгоды Azure для сервера S'L на Azure VM и соответствовать требованиям, у вас есть три варианта:

- Предоставить виртуальную машину, используя изображение сервера собственной лицензии с помощью Azure Marketplace. Эта опция доступна только для клиентов, имеющих корпоративное соглашение.
- Предоставить виртуальную машину с помощью изображения сервера с оплатой по мере использования в S'L Server из Azure Marketplace и активировать гибридную выгоду Azure.
- Самоустановка сервера S'L на Azure VM, вручную [зарегистрируйтесь в поставщике ресурсов S'L VM](virtual-machines-windows-sql-register-with-resource-provider.md)и активируйте гибридную выгоду Azure.

Тип лицензии сервера S'L устанавливается при подготовке VM. Вы можете изменить его в любое время после этого. Переключение между лицензионными моделями не требует простоев, не перезаменяет VM или сервис S'L Server, не добавляет дополнительных затрат и вступает в силу немедленно. На самом деле, активация Azure Hybrid Benefit *снижает* затраты.

## <a name="prerequisites"></a>Предварительные требования

Изменение модели лицензирования вашего S'L Server VM имеет следующие требования: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- [VM сервера S'L,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) зарегистрированного в [поставщике ресурсов S'L VM.](virtual-machines-windows-sql-register-with-resource-provider.md)
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) является обязательным условием для использования [гибридной выгоды Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/) 


## <a name="vms-already-registered-with-the-resource-provider"></a>ВМ, уже зарегистрированные у поставщика ресурсов 

# <a name="portal"></a>[Портал](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Изменить модель лицензии можно непосредственно с портала: 

1. Откройте [портал Azure](https://portal.azure.com) и откройте [ресурс виртуальных машин для](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) вашего Сервера VM. 
1. Выберите **настройку** под **настройками.** 
1. Выберите опцию **Azure Hybrid Benefit** и выберите флажок, чтобы подтвердить наличие лицензии на сервер с программным обеспечением. 
1. Выберите **Применить** в нижней части страницы **Настройка.** 

![Гибридная выгода Azure на портале](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Для изменения модели лицензии можно использовать Azure CLI.  


**Гибридная выгода Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Оплата, как вы идете:** 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Аварийное восстановление**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Вы можете использовать PowerShell для изменения модели лицензии.

**Преимущество гибридного использования Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Платите, как вы идете**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Аварийное восстановление** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>ВМ, не зарегистрированные у поставщика ресурсов

Если вы зарезервировали VM сервера S'L от изображений Azure Marketplace с оплатой по мере того, как тип лицензии S'L Server будет платным по мере того, как он будет платить по мере того. Если вы приготовили VM сервера S'L, используя изображение собственной лицензии из Azure Marketplace, то типом лицензии будет AHUB. Все VMs-M сервера S'L, подготовленные по умолчанию (оплата по мере использования) или принесите собственные лицензии на Azure Marketplace изображения, будут автоматически зарегистрированы в поставщике ресурсов S'L VM, чтобы они могли изменить [тип лицензии.](#vms-already-registered-with-the-resource-provider)

Вы имеете право самостоятельно установить сервер S'L на Azure VM через Azure Hybrid Benefit. Эти [VMs следует зарегистрировать у поставщика ресурсов S'L VM,](virtual-machines-windows-sql-register-with-resource-provider.md) установив лицензию S'L Server в качестве azure Hybrid Benefit, чтобы указать использование гибридных преимуществ Azure в соответствии с условиями продукта Майкрософт.

Вы можете изменить тип лицензии в качестве системы оплаты по мере использования или гибридной выгоды Azure только в том случае, если S'L Server VM зарегистрирован ажиотажем VM-ресурса.

## <a name="remarks"></a>Remarks

- Клиенты-поставщики облачных решений Azure (CSP) могут использовать гибридную выгоду Azure, сначала развернув VM с оплатой по мере использования, а затем преобразовав его для собственной лицензии, если у них есть активная гарантия программного обеспечения.
- Если вы отбросите ресурс S'L Server VM, вы вернетесь к жестко закодированной настройке лицензии изображения. 
- Возможность изменения лицензионной модели является особенностью поставщика ресурсов S'L VM. Развертывание изображения Azure Marketplace через портал Azure автоматически регистрирует VM сервера S'L с поставщиком ресурсов. Но клиентам, которые самостоятельно устанавливают сервер S'L Server, необходимо будет вручную [зарегистрировать свой S'L Server VM.](virtual-machines-windows-sql-register-with-resource-provider.md) 
- Добавление VM сервера S'L в набор доступности требует воссоздания VM. Таким образом, все ВМ, добавленные в набор доступности, будут возвращаться к типу лицензии по умолчанию с оплатой по мере выполнения. Azure Hybrid Benefit необходимо будет снова включения. 


## <a name="limitations"></a>Ограничения

Изменение модели лицензии:
   - Доступно только для клиентов с [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Поддерживается только для стандартных и корпоративных изданий сервера S'L. Изменения лицензий для Express, Web и Developer не поддерживаются. 
   - Поддерживается только для виртуальных машин, развернутых в модели Azure Resource Manager. Виртуальные машины, развернутые через классическую модель, не поддерживаются. 
   - Доступно только для общедоступных или лазурных правительственных облаков. 
   - Поддерживается только на виртуальных машинах, которые имеют единый сетевой интерфейс (NIC). 


## <a name="known-errors"></a>Известные ошибки

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Ресурс "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<ресурс-группа>" в\<ресурсной группе "Ресурсная группа>" не найден.

Эта ошибка возникает при попытке изменить модель лицензии на VM сервера S'L, который не был зарегистрирован в поставщике ресурсов S'L VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Вам нужно будет зарегистрировать подписку у поставщика ресурсов, а затем [зарегистрировать свой S'L Server VM с поставщиком ресурсов.](virtual-machines-windows-sql-register-with-resource-provider.md) 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Виртуальная машина\<'\>vmname ' имеет более чем один NIC связанных

Эта ошибка происходит на виртуальных машинах, которые имеют более одного NIC. Удалите один из NI, прежде чем изменить модель лицензирования. Хотя можно добавить NIC обратно в VM после изменения модели лицензии, операции на портале Azure, такие как автоматическое резервное копирование и исправление, больше не будут поддерживаться. 


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценообразованию для сервера S'L на Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Выпуск примечаний для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


