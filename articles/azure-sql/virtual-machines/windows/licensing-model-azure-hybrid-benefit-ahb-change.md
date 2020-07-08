---
title: Изменение модели лицензирования для виртуальной машины SQL в Azure
description: Узнайте, как изменить модель лицензирования для виртуальной машины SQL Server в Azure с оплаты по мере использования на использование собственной лицензии, применяя Преимущество гибридного использования Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f02f31e0fc8943682af77ca6f506d15f36e88146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668907"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Изменение модели лицензирования для виртуальной машины SQL в Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


В этой статье показано, как изменить модель лицензирования для виртуальной машины SQL Server в Azure с помощью нового поставщика ресурсов виртуальной машины SQL Server **Microsoft.SqlVirtualMachine**.

Для виртуальной машины с SQL Server поддерживаются три модели лицензирования: с оплатой по мере использования, Преимущество гибридного использования Azure (AHB) и аварийное восстановление (DR). Вы можете изменить модель лицензирования для виртуальной машины SQL Server с помощью портала Azure, Azure CLI или PowerShell. 

- Модель **с оплатой по мере использования** означает, что стоимость лицензии SQL Server включается в стоимость каждой секунды выполнения виртуальной машины Azure.
- [Преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) позволяет применять собственную лицензию SQL Server для виртуальной машины с SQL Server. 
- Тип лицензии **аварийное восстановление** используется только для [бесплатной реплики аварийного восстановления](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) в Azure. 

Преимущество гибридного использования Azure позволяет применять на виртуальных машинах Azure лицензии SQL Server с поддержкой Software Assurance ("соответствующие лицензии"). В рамках Преимущества гибридного использования Azure клиентам не нужно платить за лицензию SQL Server на виртуальной машине. При этом клиенты должны платить за использование базовых облачных вычислительных ресурсов (базовый тариф), хранилищ и резервных копий. Кроме того, клиент оплачивает входящий и исходящий трафик, связанный с использованием служб (если это применимо).

Условия использования продуктов Майкрософт определяют следующее. Клиенты должны указать, что используют Базу данных SQL Azure (Управляемый экземпляр, Эластичный пул и Отдельная база данных), Фабрику данных Azure, SQL Server Integration Services или виртуальные машины SQL Server в рамках Преимущества гибридного использования Azure для SQL Server при настройке рабочих нагрузок в Azure.

Обеспечить соблюдение требований и сообщить об использовании Преимущества гибридного использования Azure для SQL Server на виртуальной машине Azure можно тремя способами.

- Подготовьте виртуальную машину на основе образа SQL Server с использованием собственной лицензии из Azure Marketplace. Этот вариант сейчас доступен только клиентам с Соглашением Enterprise.
- Подготовьте виртуальную машину на основе образа SQL Server с оплатой по мере использования из Azure Marketplace, а затем активируйте Преимущество гибридного использования Azure.
- Самостоятельно установите SQL Server на виртуальную машину Azure, а затем вручную [зарегистрируйте этот экземпляр в поставщике ресурсов виртуальных машин SQL](sql-vm-resource-provider-register.md) и активируйте Преимущество гибридного использования Azure.

Тип лицензии для SQL Server можно настроить при подготовке виртуальной машины или в любое время позже. Переключение между моделями лицензирования выполняется без простоя, перезапуска виртуальной машины или службы SQL Server и дополнительных затрат, вступая в силу немедленно. По сути, активация Преимущества гибридного использования Azure *сокращает* затраты.

## <a name="prerequisites"></a>Предварительные требования

Для изменения модели лицензирования для виртуальной машины SQL Server требуется следующее: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- [Виртуальная машина SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision), зарегистрированная в [поставщике ресурсов виртуальных машин SQL](sql-vm-resource-provider-register.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) как обязательное условие для использования [Преимущества гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Виртуальная машина, зарегистрированная в поставщике ресурсов 

# <a name="the-azure-portal"></a>[Портал Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Модель лицензирования можно изменить непосредственно на портале, сделав следующее: 

1. Откройте [портал Azure](https://portal.azure.com) и найдите [ресурс виртуальных машин SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) для нужной виртуальной машины SQL Server. 
1. Выберите **Конфигурация** в разделе **Параметры**. 
1. Выберите вариант **Преимущество гибридного использования Azure** и установите флажок, чтобы подтвердить наличие лицензии SQL Server с поддержкой Software Assurance. 
1. Щелкните **Применить** в нижней части страницы **Конфигурация**. 

![Преимущество гибридного использования Azure на портале](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

Вы можете изменить модель лицензирования с помощью Azure CLI.  


**Преимущество гибридного использования Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**С оплатой по мере использования** 

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

Вы можете изменить модель лицензирования с помощью PowerShell.

**Преимущество гибридного использования Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Оплата по мере использования**

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

## <a name="vms-not-registered-with-the-resource-provider"></a>Виртуальная машина, не зарегистрированная в поставщике ресурсов

Если вы подготовили виртуальную машину SQL Server на основе образа с оплатой по мере использования из Azure Marketplace, для нее будет применяться лицензия SQL Server с оплатой по мере использования. Если вы подготовили виртуальную машину SQL Server на основе образа с использованием собственной лицензии из Azure Marketplace, для нее будет включено Преимущество гибридного использования Azure. Все виртуальные машины SQL Server, подготовленные на основе стандартного образа с оплатой по мере использования или образа с использованием собственной лицензии из Azure Marketplace, автоматически регистрируются в поставщике ресурсов виртуальной машины SQL. Это позволяет изменять для них [тип лицензии](#vms-already-registered-with-the-resource-provider).

Самостоятельно установить SQL Server на виртуальной машине Azure вы сможете только с применением Преимущества гибридного использования Azure. [Зарегистрируйте виртуальные машины в поставщике ресурсов виртуальных машин SQL](sql-vm-resource-provider-register.md), указав в качестве лицензии Преимущество гибридного использования Azure для SQL Server, чтобы сообщить об использовании этого варианта в соответствии с условиями использования продуктов корпорации Майкрософт.

Тип лицензии для виртуальной машины SQL Server можно изменить на оплату по мере использования или Преимущество гибридного использования Azure, только если виртуальная машина SQL Server зарегистрирована в поставщике ресурсов виртуальных машин SQL.

## <a name="remarks"></a>Remarks

- Клиенты, участвующие в программе "Поставщик облачных речений Azure" (CSP), могут использовать Преимущество гибридного использования Azure, предварительно развернув виртуальную машину с оплатой по мере использования, а затем переключив ее на использование собственной лицензии, если есть активная поддержка Software Assurance.
- Если вы удалите ресурс виртуальной машины SQL Server, параметры лицензирования будут возвращены к состоянию, которое прописано в коде для соответствующего образа. 
- Изменение модели лицензирования — это возможность поставщика ресурсов виртуальных машин SQL. Развертывание образа из Azure Marketplace через портал Azure предусматривает автоматическую регистрацию виртуальной машины SQL Server в поставщике ресурсов. Но тем клиентам, которые самостоятельно устанавливают SQL Server, нужно будет вручную [зарегистрировать виртуальные машины SQL Server](sql-vm-resource-provider-register.md). 
- Для добавления виртуальной машины SQL Server в группу доступности необходимо повторно создать эту виртуальную машину. Это означает, что все добавленные в группу доступности виртуальные машины снова получат тип лицензии с оплатой по мере использования, который настроен по умолчанию. После этого нужно будет снова включить Преимущество гибридного использования Azure. 


## <a name="limitations"></a>Ограничения

Изменение модели лицензирования связано со следующими ограничениями:
   - Эта возможность доступна только клиентам, участвующим в программе [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Поддерживаются только выпуски SQL Server Standard и Enterprise. Изменять тип лицензии для выпусков Express, Web и Developer нельзя. 
   - Поддерживаются только виртуальные машины, развернутые с помощью модели развертывания с Azure Resource Manager. Виртуальные машины, развернутые с помощью классической модели развертывания, не поддерживаются. 
   - Доступно только для общедоступных облаков и Azure для государственных организаций. 
   - Поддерживается только на виртуальных машинах с одним сетевым интерфейсом. 


## <a name="known-errors"></a>Известные ошибки

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Ресурс Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group> в группе ресурсов \<resource-group> не найден

Эта ошибка возникает при попытке изменить модель лицензирования для виртуальной машины SQL Server, которая не зарегистрирована в поставщике ресурсов виртуальных машин SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Вам необходимо зарегистрировать подписку в поставщике ресурсов, а затем и [виртуальную машину SQL Server](sql-vm-resource-provider-register.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>С виртуальной машиной \<vmname\> связано несколько сетевых карт

Такая ошибка возникает на виртуальных машинах с несколькими сетевыми картами. Удалите одну из сетевых карт, прежде чем изменить модель лицензирования. После изменения модели лицензирования вы можете обратно добавить сетевую карту для виртуальной машины, но при этом на портале Azure не будут поддерживаться такие операции, как автоматическое резервное копирование и установка исправлений. 


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](sql-server-on-azure-vm-iaas-what-is-overview.md).
* [Вопросы и ответы об SQL Server на виртуальных машинах Windows](frequently-asked-questions-faq.md)
* [Руководство по ценам для виртуальных машин SQL Server в Azure](pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](../../database/doc-changes-updates-release-notes.md)


