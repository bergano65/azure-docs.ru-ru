---
title: Зарегистрируйтесь у поставщика ресурсов S'L VM
description: Зарегистрируйте виртуальную машину Azure S'L Server с помощью поставщика ресурсов S'L VM, чтобы включить функции для виртуальных технологий сервера S'L, развернутые за пределами Azure Marketplace, а также соответствие требованиям и улучшенную управляемость.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d9c1cff53d5d0f0385d3d61938c7fb6309efb7b1
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985394"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Зарегистрируйте виртуальную машину сервера S'L в Azure с помощью поставщика ресурсов S'L VM

В этой статье описывается, как зарегистрировать виртуальную машину (VM) в Azure с поставщиком ресурсов S'L VM. Регистрация у поставщика ресурсов создает _виртуальный_ машинный ресурс **S'L** в рамках подписки, которая является отдельным ресурсом от ресурса виртуальной машины. Нерегистрирование вашего S'L Server VM от поставщика ресурсов удалит _виртуальный машинный ресурс_ **S'L,** но не отбросит фактическую виртуальную машину. 

Развертывание изображения S'L Server VM Azure Marketplace через портал Azure автоматически регистрирует VM сервера S'L с поставщиком ресурсов. Однако, если вы решите самостоятельно установить сервер S'L на виртуальную машину Azure или предоставить виртуальную машину Azure из пользовательского VHD, вам следует зарегистрировать свой S'L Server VM с поставщиком ресурсов для:

- **Преимущества функции**: Регистрация вашего S'L Server VM с поставщиком ресурсов разблокирует [автоматическое](virtual-machines-windows-sql-automated-patching.md)исправление, [автоматизированное резервное копирование,](virtual-machines-windows-sql-automated-backup-v2.md)а также возможности мониторинга и управляемости. Он также открывает [лицензирования](virtual-machines-windows-sql-ahb.md) и [гибкости издания.](virtual-machines-windows-sql-change-edition.md) Ранее эти функции были доступны только для изображений VM сервера S'L Server, развернутых на Azure Marketplace. 

- **Соответствие**требованиям : Регистрация с поставщиком ресурсов S'L VM предлагает упрощенный метод выполнения требования уведомлять корпорацию Майкрософт о включении гибридной выгоды Azure, как это указано в условиях продукта. Этот процесс сводит на нет необходимость управления регистрационными формами лицензирования для каждого ресурса.  

- **Бесплатное управление**: Регистрация с поставщиком ресурсов S'L VM во всех трех режимах управления является абсолютно бесплатной. Дополнительные затраты, связанные с поставщиком ресурсов или с изменением режимов управления, не связаны ни с поставщиком ресурсов. 

- **Упрощенное управление лицензиями**: Регистрация с поставщиком ресурсов S'L VM упрощает управление лицензиями сервера S'L Server и позволяет быстро идентифицировать VMs-сервер с помощью портала Azure Hybrid Benefit, включенного с помощью [портала Azure,](virtual-machines-windows-sql-manage-portal.md)Az CLI или PowerShell: 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Для того, чтобы использовать поставщика ресурсов S'L VM, необходимо сначала [зарегистрировать подписку у поставщика ресурсов,](#register-subscription-with-rp)что дает поставщику ресурсов возможность создавать ресурсы в рамках этой конкретной подписки.

Для получения более подробной информации о преимуществах использования поставщика ресурсов S'L VM, посмотрите это видео [channel9:](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Выгода от поставщика ресурсов ВМ при самостоятельной установке сервера S'L на Azure - Microsoft Channel 9 Видео"></iframe>


## <a name="prerequisites"></a>Предварительные требования

Для регистрации ВМ сервера S'L у поставщика ресурсов необходимо: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- Модель ресурсов Azure [S'L Server VM](virtual-machines-windows-portal-sql-server-provision.md) развернута в общедоступном или правительственном облаке Azure. 
- Последняя версия [Azure CLI](/cli/azure/install-azure-cli) или [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Режимы управления

Если [расширение S'L IaaS](virtual-machines-windows-sql-server-agent-extension.md) еще не установлено, регистрация в поставщике ресурсов S'L VM автоматически устанавливает расширение S'L Server IaaS в одном из трех режимов управления, указанных в процессе регистрации. Не указывая режим управления установят расширение S'L IaaS в полном режиме управления.  

Если расширение S'L IaaS уже установлено вручную, то оно уже находится в полном режиме управления, и регистрация у поставщика ресурсов в полном режиме не будет перезапускать сервис S'L Server.

Три режима управления:

- **Легкий** режим не требует перезапуска сервера S'L, но поддерживает только изменение типа лицензии и издание сервера S'L. Используйте эту опцию для VMs-сервера s'L Server с несколькими экземплярами или участвуя в экземпляре кластера сбоев (FCI). При использовании легкого режима не влияет на память или процессор, а связанных с ней затрат нет. Рекомендуется сначала зарегистрировать VM сервера S'L Server в легком режиме, а затем перейти в штатное окно во время запланированного окна обслуживания.  

- **Полный** режим обеспечивает всю функциональность, но требует перезагрузки сервера S'L и разрешения системного администратора. Это опция, которая установлена по умолчанию при установке расширения S'L IaaS вручную. Используйте его для управления VM сервера S'L с одним экземпляром. Полный режим устанавливает два окна службы, которые имеют минимальное влияние на память и процессор - они могут контролироваться через менеджер задач. Использование полного режима управляемости не сопряжено с расходами. 

- Режим **NoAgent** предназначен для S'L Server 2008 и S'L Server 2008 R2, установленных на Windows Server 2008. При использовании режима NoAgent не влияет на память или процессор. Использование режима управляемости NoAgent не сопряжено с расходами. 

Вы можете просмотреть текущий режим вашего агента Сервера IaaS с помощью PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Регистрация подписки с RP

Для того, чтобы зарегистрировать свой S'L Server VM с поставщиком ресурсов S'L VM, необходимо сначала зарегистрировать подписку у поставщика ресурсов. Это дает поставщику ресурсов S'L VM возможность создавать ресурсы в рамках подписки.  Это можно сделать с помощью портала Azure, ClI Azure или PowerShell.

### <a name="azure-portal"></a>Портал Azure

1. Откройте портал Azure и перейдите на **все службы.** 
1. Перейдите на **подписку** и выберите интересуемую подписку.  
1. На странице **подписки** перейдите к **поставщикам ресурсов**. 
1. Введите **sql** в фильтр, чтобы привлечь поставщиков ресурсов, связанных с S'L. 
1. Выберите **регистрацию,** **перерегистрацию**или **отменить регистрацию** для поставщика **Microsoft.SqlVirtualMachine,** в зависимости от желаемого действия. 

![Изменение поставщика](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Командная строка

Зарегистрируйте поставщика ресурсов S'L VM в подписке Azure, используя az CLI или PowerShell. 

# <a name="az-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Регистрация S'L VM с RP 

### <a name="lightweight-management-mode"></a>Легкий режим управления

Если расширение [агента Сервера IaaS](virtual-machines-windows-sql-server-agent-extension.md) не было установлено на VM, то рекомендуется зарегистрироваться в поставщике ресурсов S'L VM в легком режиме. Это позволит установить расширение S'L IaaS в [легком режиме](#management-modes) и предотвратить перезапуск службы S'L Server. Затем можно перейти в штат новейшую систему в любое время, но при этом будет перезапустить службу Сервера S'L, поэтому рекомендуется подождать до запланированного окна обслуживания. 

Обеспечить тип лицензии S'L Server как`PAYG`либо с оплатой по мере`AHUB`использования () для оплаты за`DR`использование, Azure Hybrid Benefit ( ) использовать собственную лицензию, или аварийное восстановление () для активации [бесплатной лицензии РЕПЛИКи DR.](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)

Случаи кластера Failover и развертывания нескольких экземпляров могут быть зарегистрированы только в поставщике ресурсов S'L VM в легком режиме. 

# <a name="az-cli"></a>[Azure CLI](#tab/bash)

Зарегистрируйте S'L Server VM в легком режиме с Az CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Зарегистрируйте S'L Server VM в легком режиме с PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Полный режим управления


Если расширение S'L IaaS уже было установлено в VM вручную, то вы можете зарегистрировать VM сервера S'L в полном режиме без перезапуска службы S'L Server. **Однако, если расширение S'L IaaS не было установлено, регистрация в полном режиме установит расширение S'L IaaS в полном режиме и перезапустит сервис S'L Server. Пожалуйста, будьте осторожны.**


Чтобы зарегистрировать Ваш S'L Server VM непосредственно в полном режиме (и, возможно, перезапустить службу S'L Server), используйте следующую команду PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Режим управления NoAgent 

Сервер 2008 и 2008 R2, установленный на Windows Server 2008 _(не R2),_ может быть зарегистрирован в поставщике ресурсов S'L VM в [режиме NoAgent.](#management-modes) Эта опция обеспечивает соответствие требованиям и позволяет контролировать VM сервера S'L server на портале Azure с ограниченной функциональностью.

Укажите `AHUB` `PAYG`либо `DR` , , или как `SQL2008-WS2008` **sqlLicenseType**, и или как `SQL2008R2-WS2008` **sqlImageOffer**. 

Чтобы зарегистрировать свой S'L Server 2008 или 2008 R2 на примере Windows Server 2008, используйте следующий фрагмент кода Az CLI или PowerShell: 


# <a name="az-cli"></a>[Azure CLI](#tab/bash)

Зарегистрируйте свой VM-сервер 2008 в режиме NoAgent с помощью Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Зарегистрируйте свой S'L Server 2008 R2 VM в режиме NoAgent с Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Регистрация S'L Server 2008 VM в режиме NoAgent с PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Регистрация S'L Server 2008 R2 VM в режиме NoAgent с PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Обновление до полного режима управления 

ВМ с *легким* расширением IaaS, установленные на сервере S'L Server, могут обновить режим до _полного_ с помощью портала Azure, Az CLI или PowerShell. VMs-серверв в режиме _NoAgent_ может быть обновлен до _полного_ уровня после обновления ОС до Windows 2008 R2 и выше. Это не возможно, чтобы понизить - для этого, вам нужно будет [отменить регистрацию](#unregister-vm-from-rp) S'L Server VM от поставщика ресурсов ВМ S'L. Это позволит удалить **ресурс виртуальной машины S'L,** _resource_но не будет удалять фактическую виртуальную машину. 

Вы можете просмотреть текущий режим вашего агента Сервера IaaS с помощью PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Для обновления режима агента до полного: 


### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).
1. Перейдите на ресурс [виртуальных машин S'L.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 
1. Выберите виртуальную машину сервера S'L Server и выберите **Обзор.** 
1. Для VMs-сервера с режимом NoAgent или легкого IaaS выберите только тип лицензии и обновления издания доступны с помощью сообщения **расширения S'L IaaS.**

   ![Выбор для изменения режима с портала](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Выберите **i соглашаюсь перезапустить сервис S'L Server на виртуальном автоматном** флажке, а затем выберите **Подтвердите,** чтобы обновить режим IaaS до полного. 

    ![Проверить поле для согласия на перезапуск службы сервера S'L на виртуальной машине](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Командная строка

# <a name="az-cli"></a>[Azure CLI](#tab/bash)

Выполнить следующий фрагмент кода Az CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Выполнить следующий фрагмент кода PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Проверить статус регистрации
Вы можете проверить, зарегистрирован ли ваш S'L Server VM с помощью портала Azure, Azure CLI или PowerShell. 

### <a name="azure-portal"></a>Портал Azure 

1. Войдите на [портал Azure](https://portal.azure.com). 
1. Перейдите на [виртуальные машины сервера S'L](virtual-machines-windows-sql-manage-portal.md)Server.
1. Из списка выберите VM сервера S'L. Если ваш VM сервера S'L не указан здесь, он, скорее всего, не был зарегистрирован в поставщике ресурсов S'L VM. 
1. Просмотр значения под **статусом**. Если **статус** **успешно**выполнен, то VM сервера S'L был успешно зарегистрирован с поставщиком ресурсов S'L VM. 

![Проверка статуса с регистрацией S'L RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Командная строка

Проверьте текущий статус регистрации сервера VM с помощью Az CLI или PowerShell. `ProvisioningState`покажет, `Succeeded` была ли регистрация успешной. 

# <a name="az-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ошибка указывает на то, что VM сервера S'L не был зарегистрирован поставщиком ресурсов. 


## <a name="unregister-vm-from-rp"></a>Нерегистрировать VM от RP

Чтобы отменить регистрацию вашего S'L Server VM с помощью поставщика ресурсов S'L VM, удалите *ресурс* виртуальной машины СЗЛ с помощью портала Azure или Azure CLI. Удаление *ресурса* виртуальной машины S'L не удаляет VM сервера S'L. Однако используйте осторожность и внимательно следуйте за шагами, потому что при попытке удалить *ресурс*можно непреднамеренно удалить виртуальную машину. 

Для полного понижения рейтинга режима управления с помощью ресурсного провайдера S'L VM необходимо отменить с помощью s'L VM. 

### <a name="azure-portal"></a>Портал Azure

Чтобы отменить регистрацию вашего S'L Server VM с помощью ресурсного провайдера с помощью портала Azure, выполните следующие действия:

1. Войти на [портал Azure](https://portal.azure.com).
1. Перейдите на ресурс S'L Server VM. 
  
   ![Ресурс виртуальных машин СЗЛ](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Выберите **Удалить**. 

   ![Удалить поставщика ресурсов ВМ](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Введите имя виртуальной машины S'L и **очистите флажок рядом с виртуальной машиной.**

   ![Удалить поставщика ресурсов ВМ](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Неспособность очистить флажок рядом с виртуальным названием машины *полностью удалит* виртуальную машину. Очистите флажок, чтобы отменить регистрацию S'L Server VM от поставщика ресурсов, но *не удалить фактическую виртуальную машину.* 

1. Выберите **Удалить,** чтобы подтвердить удаление *виртуального машинного ресурса,* а не виртуальную машину сервера S'L. 

### <a name="command-line"></a>Командная строка

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Чтобы отменить регистрацию виртуальной машины S'L Server от поставщика ресурсов с помощью Azure CLI, используйте команду [удаления az sql vm.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) Это позволит удалить *виртуальный машинный ресурс* S'L Server, но не удалит виртуальную машину. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Чтобы отменить регистрацию виртуальной машины S'L Server от поставщика ресурсов с помощью Azure CLI, используйте команду [New-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) Это позволит удалить *виртуальный машинный ресурс* S'L Server, но не удалит виртуальную машину. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Ограничения

Поставщик ресурсов ВМ S'L поддерживает только:
- ВМ сервера S'L, развернутые через менеджер ресурсов Azure. ВМ сервера S'L, развернутые через классическую модель, не поддерживаются. 
- ВМ-сервера S'L, развернутые в общедоступном или правительственном облаке Azure. Развертывание в других частных или правительственных облаках не поддерживается. 


## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы 

**Должен ли я зарегистрировать свой VM сервера S'L, подготовленный на изображении сервера S'L в Azure Marketplace?**

Нет. Корпорация Майкрософт автоматически регистрирует вдыхать в Azure Marketplace вгруппаных выв.М. Регистрация в поставщике ресурсов S'L VM требуется только в том случае, если VM *не* был подготовлен из изображений сервера S'L в Azure Marketplace и s'L Server был самоустановлен.

**Доступен ли поставщик ресурсов виртуальной машины SQL для всех клиентов?** 

Да. Клиенты должны зарегистрировать свои VMs-сервера s'L с поставщиком ресурсов S'L VM, если они не использовали изображение сервера S'L из Azure Marketplace и вместо этого самостоятельно установили сервер S'L, или если они принесли свой пользовательский VHD. ВМ, принадлежащие ко всем типам подписок (Direct, Enterprise Agreement и Cloud Solution Provider), могут зарегистрироваться в поставщике ресурсов S'L VM.

**Должен ли я зарегистрироваться в поставщике ресурсов S'L VM, если в моем S'L Server VM уже установлено расширение Сервера IaaS?**

Если ваш VM сервера S'L установлен самостоятельно, а не подготовлен из изображений сервера S'L в Azure Marketplace, вам следует зарегистрироваться в поставщике ресурсов S'L VM, даже если вы установили расширение S'L Server IaaS. Регистрация у поставщика ресурсов S'L VM создает новый ресурс типа Microsoft.SqlVirtualMachines. Установка расширения S'L Server IaaS не создает этот ресурс.

**Что такое режим управления по умолчанию при регистрации в поставщике ресурсов S'L VM?**

Режим управления по умолчанию при регистрации в поставщике ресурсов S'L VM *полон.* Если свойство управления сервером S'L Server не установлено при регистрации в поставщике ресурсов S'L VM, режим будет установлен как полное управляемость, и ваш сервис S'L Server будет перезавержен. Рекомендуется сначала зарегистрироваться в поставщике ресурсов S'L VM в легком режиме, а затем полностью перейти на полное обновление во время окна обслуживания. 

**Каковы предпосылки для регистрации в поставщике ресурсов S'L VM?**

Нет никаких предпосылок для регистрации в поставщике ресурсов S'L VM в легком режиме или без агента. Предпосылкой для регистрации в полном режиме является установка на VM расширения S'L Server IaaS, так как в противном случае услуга S'L Server будет перезастрана. 

**Могу ли я зарегистрироваться в поставщике ресурсов S'L VM, если у меня нет расширения S'L Server IaaS, установленного на VM?**

Да, вы можете зарегистрироваться у поставщика ресурсов S'L VM в легком режиме управления, если на VM нет расширения S'L Server IaaS. В легком режиме поставщик ресурсов S'L VM будет использовать консольное приложение для проверки версии и выпуска экземпляра сервера S'L. 

Режим управления по умолчанию s'L при регистрации с поставщиком ресурсов S'L VM _полон._ Если свойство S'L Management не устанавливается при регистрации с поставщиком ресурсов S'L VM, то режим будет установлен как полное управляемость. Рекомендуется сначала зарегистрироваться в поставщике ресурсов S'L VM в легком режиме, а затем полностью перейти на полное обновление во время окна обслуживания. 

**Будет ли регистрация в поставщике ресурсов S'L VM устанавливать агент на моем VM?**

Нет. Регистрация у поставщика ресурсов S'L VM приведет только к созданию нового ресурса метаданных. Он не будет устанавливать агент на VM.

Расширение S'L Server IaaS необходимо только для обеспечения полной управляемости. Обновление режима управляемости с легкого на полный установить расширение S'L Server IaaS и перезапустить сервер S'L Server.

**Будет ли регистрация в провайдере ресурсов S'L Server VM перезапустить s-L Server на моем VM?**

Это зависит от режима, указанного во время регистрации. Если указан легкий или режим NoAgent, то услуга S'L Server не будет перезаскана. Тем не менее, указание режима управления как полного или оставить режим управления пустым, установит расширение S'L IaaS в режиме полного управления, что приведет к перезапуску службы S'L Server. 

**В чем разница между легкими и без агентами режимами управления при регистрации в поставщике ресурсов S'L VM?** 

Режим управления без агента доступен только для сервера S'L 2008 и S'L Server 2008 R2 на Windows Server 2008. Это единственный доступный режим управления для этих версий. Для всех других версий сервера S'L, два доступных режима управления являются легкими и полными. 

Режим без агента требует, чтобы клиент устанавливал версию сервера s'L Server и свойства издания. Легкий режим запрашивает VM, чтобы найти версию и издание экземпляра сервера S'L.

**Могу ли я зарегистрироваться в поставщике ресурсов S'L VM, не указывая тип лицензии S'L Server?**

Нет. Тип лицензии S'L Server не является дополнительным свойством при регистрации в поставщике ресурсов S'L VM. При регистрации в поставщике ресурсов S'L VM во всех режимах управления (без агента, легкого и полного) необходимо установить тип лицензии S'L Server в качестве гибридной выгоды с оплатой по мере использования или Azure.

**Могу ли я обновить расширение сервера IaaS с режима без агента до полного?**

Нет. Обновление режима управляемости до полного или легкого недоступно для режима без агента. Это техническое ограничение Windows Server 2008. Вам нужно будет обновить ОС сначала до Windows Server 2008 R2 или больше, а затем вы сможете перейти в полный режим управления. 

**Могу ли я обновить расширение сервера IaaS с легкого в полный?**

Да. Обновление режима управляемости от легкого до полного поддерживается с помощью PowerShell или портала Azure. Требуется перезапуск службы S'L Server.

**Могу ли я понизить уровень расширения сервера IaaS с полного режима до режима без агента или легкого управления?**

Нет. Понижение режима управления расширением сервера IaaS не поддерживается. Режим управляемости не может быть понижен с полного режима в легкий или неагентный режим, и он не может быть понижен с легкого режима в режим без агента. 

Чтобы изменить режим управляемости от полной управляемости, [отрегистрируйте](#unregister-vm-from-rp) виртуальную машину S'L Server от поставщика ресурсов S'L Server, отбросив ресурс s'L Server, и перерегистрируйте *VM* сервера S'L с поставщиком ресурсов S'L VM снова в другом режиме управления.

**Могу ли я зарегистрироваться у поставщика ресурсов S'L VM на портале Azure?**

Нет. Регистрация в поставщике ресурсов S'L VM недоступна на портале Azure. Регистрация в поставщике ресурсов S'L VM поддерживается только с помощью Azure CLI или PowerShell. 

**Могу ли я зарегистрировать VM у поставщика ресурсов S'L VM до установки s'L Server?**

Нет. Для успешной регистрации в ресурсном провайдере ВМ VM vM должен быть по крайней мере один экземпляр S'L Server (Database Engine). Если на VM нет экземпляра сервера S'L, новый ресурс Microsoft.SqlVirtualMachine будет находиться в неработаемом состоянии.

**Могу ли я зарегистрировать VM у поставщика ресурсов S'L VM, если существует несколько экземпляров S'L Server?**

Да. Поставщик ресурсов ВМ ссистемой будет регистрировать только один экземпляр S'L Server (движок базы данных). Поставщик ресурсов ВМ s'L зарегистрирует экземпляр сервера по умолчанию в случае нескольких экземпляров. Если экземпляра по умолчанию нет, поддержка поддерживается только в легком режиме. Для обновления от легкого к режиму полного управления должен существовать либо экземпляр сервера по умолчанию, либо VM должен иметь только один экземпляр с названием S'L Server.

**Могу ли я зарегистрировать экземпляр кластера сотказа сервера S'L Server с поставщиком ресурсов S'L VM?**

Да. Случаи сбоя в кластере S'L Server на Azure VM могут быть зарегистрированы в поставщике ресурсов S'L VM в легком режиме. Тем не менее, случаи сбоя s'L Server не могут быть переведены в режим полного управления.

**Могу ли я зарегистрировать свой VM у поставщика ресурсов S'L VM, если настроена группа всегда на доступность?**

Да. Нет никаких ограничений для регистрации экземпляра S'L Server на Azure VM с поставщиком ресурсов S'L VM, если вы участвуете в конфигурации группы всегда на доступность.

**Какова стоимость регистрации в поставщике ресурсов S'L VM или при переходе в режим полной управляемости?**
Нет. Плата за регистрацию в поставщике ресурсов S'L VM или использование любого из трех режимов управляемости не взимается. Управление VM сервером S'L с поставщиком ресурсов является абсолютно бесплатным. 

**Каково влияние использования различных режимов управляемости?**
При использовании *режимов Управления NoAgent* и *легкого* управления нет никакого влияния. При использовании *полного* режима управляемости от двух служб, установленных на ОС, наблюдается минимальное воздействие. Они могут контролироваться с помощью диспетчера задач и видеть во встроенной консоли Службы Windows. 

Два названия служб:
- `SqlIaaSExtensionQuery`(Название дисплея - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension`(Название дисплея - `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценообразованию для сервера S'L на Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Выпуск примечаний для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
