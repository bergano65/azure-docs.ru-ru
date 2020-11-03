---
title: Установка расширения агента IaaS SQL Server
description: Зарегистрируйте виртуальную машину Azure SQL Server с помощью поставщика ресурсов виртуальной машины SQL, чтобы включить функции для SQL Server виртуальных машин, развернутых за пределами Azure Marketplace, а также для обеспечения соответствия и улучшенной управляемости.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286140"
---
# <a name="install-sql-server-iaas-agent-extension"></a>Установка расширения агента IaaS SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Регистрация SQL Server виртуальной машины с помощью поставщика ресурсов виртуальной машины SQL устанавливает [расширение агента SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). 

В этой статье описывается регистрация одной SQL Server виртуальной машины с помощью поставщика ресурсов виртуальной машины SQL. Кроме того, можно зарегистрировать все SQL Server виртуальные машины [автоматически](sql-vm-resource-provider-automatic-registration.md) или [создать скрипты в пакетном](sql-vm-resource-provider-bulk-register.md)режиме.


## <a name="overview"></a>Обзор

При регистрации в поставщике ресурсов устанавливается [расширение агента SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) , а также в рамках подписки создается _ресурс_ **виртуальной машины SQL** , который является _отдельным_ ресурсом из ресурса виртуальной машины. При отмене регистрации виртуальной машины SQL Server в поставщике ресурсов будет удален _ресурс_ **виртуальной машины SQL** , но не будет удалена фактическая виртуальная машина.

Развертывание образа виртуальной машины SQL Server из Azure Marketplace через портал Azure предусматривает автоматическую регистрацию виртуальной машины SQL Server в поставщике ресурсов. Однако если вы решили самостоятельно установить SQL Server на виртуальной машине Azure или подготовили виртуальную машину Azure из пользовательского виртуального жесткого диска, то необходимо зарегистрировать SQL Server ВИРТУАЛЬную машину с помощью поставщика ресурсов виртуальной машины SQL, чтобы установить расширение агента IaaS SQL. 

Чтобы использовать поставщик ресурсов виртуальной машины SQL, необходимо сначала [зарегистрировать подписку в поставщике ресурсов](#register-subscription-with-rp), что дает поставщику ресурсов возможность создавать ресурсы в этой подписке.

> [!IMPORTANT]
> Расширение агента IaaS SQL собирает данные для предоставления клиентам дополнительных преимуществ при использовании SQL Server в виртуальных машинах Azure. Корпорация Майкрософт не будет использовать эти данные для аудита лицензирования без согласия клиента. Дополнительные сведения см. в разделе [SQL Server о конфиденциальности](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Предварительные требования

Для регистрации виртуальной машины SQL Server с помощью поставщика ресурсов понадобится: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- Модель ресурсов Azure — это [Виртуальная машина Windows Server 2008 (или более поздняя)](../../../virtual-machines/windows/quick-create-portal.md) с [SQL Server 2008 (или более поздней)](https://www.microsoft.com/sql-server/sql-server-downloads) , развернутая в общедоступном облаке или Azure для государственных организаций 
- Последняя версия [Azure CLI](/cli/azure/install-azure-cli) или [Azure PowerShell (5,0 минимум)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Регистрация подписки с помощью поставщика ресурсов

Чтобы зарегистрировать виртуальную машину SQL Server с помощью поставщика ресурсов виртуальной машины SQL, необходимо сначала зарегистрировать подписку с помощью поставщика ресурсов. Это дает поставщику ресурсов виртуальной машины SQL возможность создавать ресурсы в подписке.  Это можно сделать с помощью портал Azure, Azure CLI или Azure PowerShell.

### <a name="azure-portal"></a>Портал Azure

1. Войдите на портал Azure и откройте раздел **Все службы**. 
1. Перейдите к разделу **Подписки** и выберите нужную подписку.  
1. На странице **Подписки** перейдите к элементу **Поставщики ресурсов**. 
1. Введите в фильтр значение **sql** , чтобы отсортировать поставщиков ресурсов, имеющих отношение к SQL. 
1. Выберите действие **Зарегистрировать** , **Перерегистрировать** или **Отменить регистрацию** для поставщика **Microsoft.SqlVirtualMachine** в зависимости от ваших намерений. 

   ![Изменение поставщика](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Командная строка

Зарегистрируйте поставщик ресурсов виртуальной машины SQL в подписке Azure, используя Azure CLI или Azure PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Регистрация с помощью RP

Существует три режима управления для [расширения агента SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Установка расширения в режиме полного управления приводит к перезапуску службы SQL Server, поэтому рекомендуется сначала установить расширение в упрощенном режиме, а затем выполнить [обновление до полного](#upgrade-to-full) периода обслуживания. 

### <a name="lightweight-management-mode"></a>Режим упрощенного управления

Используйте Azure CLI или Azure PowerShell для регистрации SQL Server виртуальной машины с помощью поставщика ресурсов и установки расширения SQL IaaS в упрощенном режиме. Это не приведет к перезапуску службы SQL Server. Затем в любое время можно выполнить обновление до полного режима, но это приведет к перезапуску службы SQL Server. Поэтому рекомендуется дождаться периода запланированного обслуживания. 

Укажите тип лицензии SQL Server: с оплатой по мере использования (`PAYG`), чтобы платить только за потребляемые ресурсы, Преимущество гибридного использования Azure (`AHUB`), чтобы использовать собственную лицензию, или аварийное восстановление (`DR`), чтобы активировать [лицензии бесплатной реплики аварийного восстановления](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Экземпляры отказоустойчивого кластера и развертывания с несколькими экземплярами можно зарегистрировать только в поставщике ресурсов виртуальной машины SQL в упрощенном режиме. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Зарегистрируйте SQL Serverную виртуальную машину в упрощенном режиме с Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Зарегистрируйте SQL Serverную виртуальную машину в упрощенном режиме с Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Режим полного управления

Регистрация виртуальной машины SQL Server в полном режиме приведет к перезапуску службы SQL Server. Будьте внимательны. 

Чтобы зарегистрировать виртуальную машину SQL Server напрямую в полном режиме (и, возможно, перезапустить службу SQL Server), используйте следующую команду Azure PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Режим управления NoAgent 

SQL Server 2008 и 2008 R2, установленные в Windows Server 2008 ( _не R2_ ), можно зарегистрировать с помощью поставщика ресурсов виртуальной машины SQL в [режиме NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Этот вариант обеспечивает соответствие и позволяет отслеживать виртуальную машину SQL Server на портале Azure с ограниченными функциональными возможностями.

Укажите `AHUB`, `PAYG` или `DR` в качестве **sqlLicenseType** и `SQL2008-WS2008` или `SQL2008R2-WS2008` в качестве **sqlImageOffer**. 

Чтобы зарегистрировать SQL Server 2008 или 2008 R2 в экземпляре Windows Server 2008, используйте следующий Azure CLI или Azure PowerShell фрагмент кода: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Зарегистрируйте виртуальную машину SQL Server 2008 в режиме агента с Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Зарегистрируйте виртуальную машину SQL Server 2008 R2 в режиме агента с Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Зарегистрируйте виртуальную машину SQL Server 2008 в режиме "без агента" с Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

Зарегистрируйте виртуальную машину SQL Server 2008 R2 в режиме агента с Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>Режим проверки

Текущий режим агента SQL Server IaaS можно просмотреть с помощью Azure PowerShell. 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Обновить до полной версии  

SQL Server виртуальные машины, на которых установлено *Облегченное* расширение IaaS, могут обновить режим до _полной_ , используя портал Azure, Azure CLI или Azure PowerShell. Виртуальные машины SQL Server в режиме _NoAgent_ можно обновить до _полного_ режима после обновления операционной системы до Windows 2008 R2 или выше. Переход на использование более ранней версии невозможен. Чтобы сделать это, необходимо [отменить регистрацию](#unregister-from-rp) виртуальной машины SQL Server в поставщике ресурсов виртуальной машины SQL. При этом будет удален _ресурс_ **виртуальной машины SQL** , но не фактическая виртуальная машина. 


### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).
1. Перейдите к ресурсу [виртуальных машин SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Выберите SQL Server виртуальную машину и щелкните **Обзор**. 
1. Для виртуальных машин IaaS для SQL Server с упрощенным режимом IaaS или NoAgent выберите сообщение **Only license type and edition updates are available with the SQL IaaS extension** (Для режима расширения SQL IaaS доступно только изменение типа лицензии и выпуска).

   ![Выбранные элементы изменения режима на портале](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Установите флажок **Я соглашаюсь перезапустить службу SQL Server на виртуальной машине** , а затем выберите **Подтвердить** , чтобы обновить режим IaaS до полного. 

    ![Флажок, подтверждающий согласие перезапуска службы SQL Server на виртуальной машине](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Командная строка

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Выполните следующий фрагмент кода Azure CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Выполните следующий фрагмент кода Azure PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Проверка состояния регистрации
Вы можете проверить, зарегистрирована ли SQL Server виртуальная машина в поставщике ресурсов виртуальной машины SQL с помощью портал Azure, Azure CLI или Azure PowerShell. 

### <a name="azure-portal"></a>Портал Azure 

1. Войдите на [портал Azure](https://portal.azure.com). 
1. Перейдите на [SQL Server виртуальные машины](manage-sql-vm-portal.md).
1. Выберите свою виртуальную машину SQL Server из списка. Если вашей виртуальной машины SQL Server здесь нет, скорее всего, она не была зарегистрирована с помощью поставщика ресурсов виртуальной машины SQL. 
1. Просмотрите значение в разделе **Состояние**. Если **Состояние** имеет значение **Успешно** , то виртуальная машина SQL Server успешно зарегистрирована с помощью поставщика ресурсов виртуальной машины SQL. 

   ![Проверка состояния при регистрации с помощью поставщика ресурсов SQL](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Командная строка

Проверьте текущее состояние регистрации виртуальной машины SQL Server с помощью Azure CLI или Azure PowerShell. Параметр `ProvisioningState` отображает состояние `Succeeded`, если регистрация прошла успешно. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ошибка указывает на то, что виртуальная машина SQL Server не зарегистрирована с помощью поставщика ресурсов. 


## <a name="unregister-from-rp"></a>Отменить регистрацию в RP

Чтобы отменить регистрацию SQL Server виртуальной машины с помощью поставщика ресурсов виртуальной машины SQL, удалите *ресурс* ВИРТУАЛЬНОЙ машины SQL, используя портал Azure или Azure CLI. При удалении *ресурса* ВИРТУАЛЬНОЙ машины SQL SQL Server виртуальная машина не удаляется. Тем не менее будьте внимательны и осторожны при выполнении этих действий, так как при попытке удалить *ресурс* можно случайно удалить виртуальную машину. 

Отмена регистрации виртуальной машины SQL с поставщиком ресурсов виртуальной машины SQL необходима для перехода в режим управления с полной на более раннюю версию. 

### <a name="azure-portal"></a>Портал Azure

Чтобы отменить регистрацию виртуальной машины SQL Server с помощью поставщика ресурсов на портале Azure, выполните следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com).
1. Перейдите к ресурсу виртуальной машины SQL. 
  
   ![Ресурс "Виртуальные машины SQL"](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Выберите команду **Удалить**. 

   ![Выберите Удалить в верхней области навигации.](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Введите имя виртуальной машины SQL и **снимите флажок рядом с этой виртуальной машиной**.

   ![Снимите флажок для виртуальной машины, чтобы предотвратить удаление реальной виртуальной машины, а затем нажмите кнопку Удалить, чтобы продолжить удаление ресурса виртуальной машины SQL.](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Если не снять флажок рядом с именем виртуальной машины, то она будет *удалена* полностью. Снимите флажок, чтобы отменить регистрацию виртуальной машины SQL Server в поставщике ресурсов и *не удалить фактическую виртуальную машину*. 

1. Выберите **Удалить** , чтобы подтвердить удаление *ресурса* виртуальной машины SQL, а не SQL Server виртуальной машины. 

### <a name="command-line"></a>Командная строка

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Чтобы отменить регистрацию SQL Server виртуальной машины в поставщике ресурсов с помощью Azure CLI, используйте команду [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) . Это приведет к удалению *ресурса* виртуальной машины SQL Server, но не удалит виртуальную машину. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Чтобы отменить регистрацию SQL Server виртуальной машины в поставщике ресурсов с помощью Azure PowerShell, используйте команду [Remove-азсклвм](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Это приведет к удалению *ресурса* виртуальной машины SQL Server, но не удалит виртуальную машину. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](sql-server-on-azure-vm-iaas-what-is-overview.md).
* [Вопросы и ответы об SQL Server на виртуальных машинах Windows](frequently-asked-questions-faq.md)  
* [Руководство по ценам для виртуальных машин SQL Server в Azure](pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](../../database/doc-changes-updates-release-notes.md)
