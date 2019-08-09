---
title: Регистрация SQL Server виртуальной машины в Azure с помощью поставщика ресурсов виртуальной машины SQL | Документация Майкрософт
description: Зарегистрируйте виртуальную машину SQL Server с помощью поставщика ресурсов виртуальной машины SQL, чтобы улучшить управляемость.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 552caf0f09dcfa291981ef73152cf4febfc4a840
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882380"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Регистрация SQL Server виртуальной машины в Azure с помощью поставщика ресурсов виртуальной машины SQL

В этой статье описывается, как зарегистрировать виртуальную машину SQL Server в Azure с помощью поставщика ресурсов виртуальной машины SQL. 

При развертывании образа виртуальной машины Azure Marketplace SQL Server с помощью портал Azure автоматически регистрирует SQL Server виртуальную машину с поставщиком ресурсов. Если вы решили самостоятельно установить SQL Server на виртуальной машине Azure, а не хотите выбрать образ из Azure Marketplace или вы подготавливаете виртуальную машину Azure из пользовательского виртуального жесткого диска с SQL Server, вы должны зарегистрировать SQL Serverную виртуальную машину в поставщике ресурсов для. :

- **Соответствие.** В соответствии с условиями продукта Майкрософт клиенты должны сообщить корпорации Майкрософт, когда они используют [преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Для этого они должны зарегистрироваться в поставщике ресурсов виртуальной машины SQL. 

- **Преимущества функций**: Регистрация SQL Server виртуальной машины с помощью поставщика ресурсов разблокирует [автоматическую установку исправлений](virtual-machines-windows-sql-automated-patching.md), [автоматическую архивацию](virtual-machines-windows-sql-automated-backup-v2.md), а также функции мониторинга и управления. Он также разблокирует гибкость [лицензирования](virtual-machines-windows-sql-ahb.md) и [выпуска](virtual-machines-windows-sql-change-edition.md) . Ранее эти функции были доступны только для SQL Server образов виртуальных машин из Azure Marketplace.

Чтобы использовать поставщик ресурсов виртуальной машины SQL, необходимо также зарегистрировать поставщик ресурсов виртуальной машины SQL в подписке. Это можно сделать с помощью портал Azure, Azure CLI или PowerShell. 

## <a name="prerequisites"></a>предварительные требования

Чтобы зарегистрировать виртуальную машину SQL Server с помощью поставщика ресурсов, вам потребуется следующее: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- [Виртуальная машина SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) и [PowerShell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Регистрация с помощью поставщика ресурсов виртуальной машины SQL
Если [расширение агента SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) не установлено на виртуальной машине, можно зарегистрировать его с помощью поставщика ресурсов ВИРТУАЛЬНОЙ машины SQL, указав упрощенный режим управления SQL. В упрощенном режиме управления SQL поставщик ресурсов виртуальной машины SQL будет автоматическая установка расширения SQL IaaS в [упрощенном режиме](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) и проверка метаданных экземпляра SQL Server. Это не приведет к перезапуску службы SQL Server. Необходимо указать тип SQL Server лицензии при регистрации с помощью поставщика ресурсов виртуальной машины SQL: "PAYG" или "AHUB".

Регистрация в поставщике ресурсов виртуальной машины SQL в [упрощенном режиме](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) обеспечит соответствие требованиям и обеспечивает гибкие возможности лицензирования, а также на месте SQL Server обновлений выпуска. Экземпляры отказоустойчивого кластера и развертывания с несколькими экземплярами могут быть зарегистрированы в поставщике ресурсов виртуальной машины SQL только в упрощенном режиме. Вы можете следовать инструкциям, приведенным на портал Azure, чтобы выполнить обновление до [полного режима](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode) и обеспечить комплексный набор функций управления с SQL Server перезапуском в любое время. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Используйте следующий фрагмент кода для регистрации в поставщике ресурсов виртуальной машины SQL, если расширение SQL Server IaaS уже установлено на виртуальной машине. Необходимо указать тип SQL Server лицензии при регистрации в поставщике ресурсов виртуальной машины SQL: с оплатой по мере использования (`PAYG`) или преимущество гибридного использования Azure (`AHUB`). 

Зарегистрируйте SQL Server виртуальную машину с помощью следующего фрагмента кода PowerShell:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)

Для платных выпусков (Enterprise или Standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type AHUB 

  ```

Для бесплатных выпусков (Developer, Web или Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Если расширение IaaS для SQL уже установлено на виртуальной машине, то регистрация в поставщике ресурсов виртуальной машины SQL выполняется просто путем создания ресурса метаданных типа Microsoft. Склвиртуалмачине/Склвиртуалмачинес. Ниже приведен фрагмент кода для регистрации в поставщике ресурсов виртуальной машины SQL, если расширение IaaS для SQL уже установлено на виртуальной машине. Необходимо указать тип SQL Server лицензии при регистрации с помощью поставщика ресурсов виртуальной машины SQL: "PAYG" или "AHUB".

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Регистрация SQL Server 2008 или 2008 R2 на виртуальных машинах Windows Server 2008

SQL Server 2008 и 2008 R2, установленные на Windows Server 2008, можно зарегистрировать в поставщике ресурсов виртуальной машины SQL в режиме [без агента](virtual-machines-windows-sql-server-agent-extension.md) . Этот параметр обеспечивает соответствие требованиям и позволяет отслеживать SQL Serverную виртуальную машину в портал Azure с ограниченными функциональными возможностями.

В следующей таблице приведены допустимые значения параметров, указанных во время регистрации.

| Параметр | Допустимые значения                                 |
| :------------------| :--------------------------------------- |
| **скллиценсетипе** | `AHUB` или `PAYG`                    |
| **склимажеоффер**  | `SQL2008-WS2008` или `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Чтобы зарегистрировать экземпляр SQL Server 2008 или 2008 R2 в экземпляре Windows Server 2008, используйте следующий фрагмент кода PowerShell или AZ CLI:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type AHUB --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Проверка состояния регистрации
Вы можете проверить, зарегистрирована ли SQL Server виртуальная машина в поставщике ресурсов виртуальной машины SQL с помощью портал Azure, Azure CLI или PowerShell. 

### <a name="azure-portal"></a>портала Azure 

1. Войдите на [портале Azure](https://portal.azure.com). 
1. Перейдите к [виртуальным машинам SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Выберите SQL Server виртуальную машину из списка. Если ваша SQL Server виртуальная машина не указана здесь, скорее всего, она не была зарегистрирована в поставщике ресурсов виртуальной машины SQL. 
1. Просмотрите значение в разделе **состояние**. Если **состояние** — " **успешно**", SQL Server виртуальная машина успешно зарегистрирована в поставщике ресурсов виртуальной машины SQL. 

![Проверка состояния с регистрацией SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Командная строка

Проверьте текущее состояние регистрации виртуальной машины SQL Server с помощью команды AZ CLI или PowerShell. `ProvisioningState`Отобразится `Succeeded` , если регистрация прошла успешно. 

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Ошибка указывает на то, что SQL Server виртуальная машина не зарегистрирована в поставщике ресурсов. 

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Регистрация поставщика ресурсов виртуальной машины SQL с подпиской 

Чтобы зарегистрировать виртуальную машину SQL Server с поставщиком ресурсов виртуальной машины SQL, необходимо зарегистрировать поставщик ресурсов в подписке. Это можно сделать с помощью портал Azure, Azure CLI или PowerShell.

### <a name="azure-portal"></a>портала Azure

1. Откройте портал Azure и перейдите ко **всем службам**. 
1. Перейдите к разделу **подписки** и выберите интересующую вас подписку.  
1. На странице **подписки** перейдите к разделу **поставщики ресурсов**. 
1. В фильтре введите **SQL** , чтобы открыть поставщики ресурсов, связанные с SQL. 
1. Выберите **Регистрация**, **Повторная регистрация**или отмена **регистрации** для поставщика **Microsoft. склвиртуалмачине** в зависимости от требуемого действия. 

![Изменение поставщика](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Командная строка

Зарегистрируйте поставщик ресурсов виртуальной машины SQL в подписке Azure, выполнив команду AZ CLI или PowerShell. 

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)
В следующем фрагменте кода поставщик ресурсов виртуальной машины SQL будет зарегистрирован в подписке Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Примечания

- Поставщик ресурсов виртуальной машины SQL поддерживает только SQL Server виртуальных машин, развернутых с помощью Azure Resource Manager. SQL Server виртуальные машины, развернутые с помощью классической модели, не поддерживаются. 
- Поставщик ресурсов виртуальной машины SQL поддерживает только SQL Server виртуальных машин, развернутых в общедоступном облаке. Развертывания в частном или правительственном облаке не поддерживаются. 
 

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы 

**Следует ли зарегистрировать виртуальную машину SQL Server, подготовленную из образа SQL Server в Azure Marketplace?**

Нет. Корпорация Майкрософт автоматически регистрирует виртуальные машины, подготовленные на основе образов SQL Server в Azure Marketplace. Регистрация в поставщике ресурсов виртуальной машины SQL требуется только в том случае, если виртуальная машина *не* была подготовлена из образов SQL Server в Azure Marketplace и SQL Server была самостоятельно установлена.

**Доступен ли поставщик ресурсов виртуальной машины SQL для всех клиентов?** 

Да. Клиенты должны зарегистрировать свои SQL Server виртуальные машины с помощью поставщика ресурсов виртуальной машины SQL, если он не использовал SQL Serverный образ из Azure Marketplace, а вместо этого самостоятельно установил SQL Server или указал свой пользовательский виртуальный жесткий диск. Виртуальные машины, принадлежащие всем типам подписок (Direct, Соглашение Enterprise и поставщик облачных решений), могут зарегистрироваться в поставщике ресурсов виртуальной машины SQL.

**Следует ли зарегистрироваться в поставщике ресурсов виртуальной машины SQL, если на моей SQL Server виртуальной машине уже установлено SQL Server расширение IaaS?**

Если SQL Serverная виртуальная машина самостоятельно установлена и не подготовлена из образов SQL Server в Azure Marketplace, вы должны зарегистрироваться в поставщике ресурсов виртуальной машины SQL, даже если вы установили расширение SQL Server IaaS. При регистрации в поставщике ресурсов виртуальной машины SQL создается новый ресурс типа Microsoft. Склвиртуалмачинес. При установке расширения IaaS SQL Server этот ресурс не создается.

**Что такое режим управления по умолчанию при регистрации в поставщике ресурсов виртуальной машины SQL?**

Режим управления по умолчанию при регистрации в поставщике ресурсов виртуальной машины SQL *заполнен*. Если свойство управления SQL Server не задано при регистрации в поставщике ресурсов виртуальной машины SQL, режим будет установлен как полный управляемый. Наличие расширения SQL Server IaaS, установленного на виртуальной машине, является необходимым условием для регистрации в поставщике ресурсов виртуальной машины SQL в режиме полного управления.

**Каковы предварительные требования для регистрации в поставщике ресурсов виртуальной машины SQL?**

Отсутствуют предварительные требования для регистрации в поставщике ресурсов виртуальной машины SQL в режиме "в упрощенном режиме" или "без агента". Предварительные требования для регистрации в поставщике ресурсов виртуальной машины SQL в полном режиме — наличие расширения SQL Server IaaS, установленного на виртуальной машине.

**Можно ли зарегистрироваться у поставщика ресурсов виртуальной машины SQL, если на виртуальной машине не установлен модуль SQL Server IaaS?**

Да, вы можете зарегистрироваться в поставщике ресурсов виртуальной машины SQL в режиме упрощенного управления, если на виртуальной машине не установлено расширение SQL Server IaaS. В упрощенном режиме поставщик ресурсов виртуальной машины SQL будет использовать консольное приложение для проверки версии и выпуска экземпляра SQL Server. 

Режим управления SQL по умолчанию при регистрации в поставщике ресурсов виртуальной машины SQL _полон_. Если свойство управления SQL не задано при регистрации с помощью поставщика ресурсов виртуальной машины SQL, то режим будет установлен как полный управляемый. Наличие расширения SQL IaaS, установленного на виртуальной машине, является необходимым условием для регистрации в поставщике ресурсов виртуальной машины SQL в режиме полного управления.

**Будет ли регистрация в поставщике ресурсов виртуальной машины SQL установить агент на виртуальной машине?**

Нет. При регистрации в поставщике ресурсов виртуальной машины SQL будет создан только новый ресурс метаданных. Агент не будет установлен на виртуальной машине.

Расширение SQL Server IaaS требуется только для обеспечения полной управляемости. Обновление режима управляемости с Lightweight на Full установит расширение SQL Server IaaS и перезагрузит SQL Server.

**Будет ли регистрация в SQL Server SQL Server перезапуска поставщика ресурсов виртуальной машины?**

Нет. При регистрации в поставщике ресурсов виртуальной машины SQL будет создан только новый ресурс метаданных. SQL Server на виртуальной машине не перезапускается. 

Перезапуск SQL Server требуется только для установки расширения SQL Server IaaS. Для обеспечения полной управляемости требуется расширение IaaS SQL Server. Обновление режима управляемости с Lightweight на Full установит расширение SQL Server IaaS и перезагрузит SQL Server.

**В чем разница между простыми и неагентными режимами управления при регистрации с помощью поставщика ресурсов виртуальной машины SQL?** 

Режим управления без агента доступен только для SQL Server 2008 и SQL Server 2008 R2 на Windows Server 2008. Это единственный доступный режим управления для этих версий. Для всех других версий SQL Server два доступных режима управления — это облегченные и полные. 

Для режима "без агента" требуется, чтобы свойства версии и выпуска SQL Server были заданы клиентом. Упрощенный режим запрашивает у виртуальной машины версию и выпуск SQL Server экземпляра.

**Можно ли зарегистрироваться в поставщике ресурсов виртуальной машины SQL в режиме упрощения или отсутствия агента с помощью Azure CLI?**

Нет. Свойство режим управления доступно только при регистрации в поставщике ресурсов виртуальной машины SQL с помощью Azure PowerShell. Azure CLI не поддерживает задание свойства управляемости SQL Server. Он всегда регистрируется в поставщике ресурсов виртуальной машины SQL в режиме по умолчанию, полностью управляемость.

**Можно ли зарегистрироваться в поставщике ресурсов виртуальной машины SQL, не указывая тип лицензии SQL Server?**

Нет. Тип лицензии SQL Server не является дополнительным свойством при регистрации в поставщике ресурсов виртуальной машины SQL. При регистрации в поставщике ресурсов виртуальной машины SQL в режиме управления (без агента, lightweight и Full) необходимо задать тип лицензии SQL Server как "Оплата по мере использования" Преимущество гибридного использования Azure или Azure CLI "с помощью PowerShell".

**Можно ли обновить расширение SQL Server IaaS из режима без агента в полный режим?**

Нет. Обновление режима управляемости до Full или lightweight недоступно для режима без агента. Это техническое ограничение Windows Server 2008.

**Можно ли обновить расширение SQL Server IaaS из упрощенного режима в полный режим?**

Да. Обновление режима управляемости с Lightweight на Full поддерживается с помощью PowerShell или портал Azure. Он требует перезапуска SQL Server.

**Можно ли понизить расширение SQL Server IaaS из полного режима в режим "без агента" или "упрощенное управление"?**

Нет. Переход на более раннюю версию режима управления расширениями SQL Server IaaS не поддерживается. Режим управляемости не может быть понижен из полного режима в режим "простой" или "без агента" и не может быть понижен из упрощенного режима в режим "без агента". 

Чтобы изменить режим управляемости с полной управляемостью, удалите расширение SQL Server IaaS. Затем удалите ресурс записи. Склвиртуалмачине и повторно зарегистрируйте SQL Server виртуальную машину с помощью поставщика ресурсов виртуальной машины SQL.

**Можно ли зарегистрироваться на поставщике ресурсов виртуальной машины SQL из портал Azure?**

Нет. Регистрация в поставщике ресурсов виртуальной машины SQL недоступна в портал Azure. Регистрация в поставщике ресурсов виртуальной машины SQL в режиме полного управления поддерживается с помощью Azure CLI или PowerShell. Регистрация в поставщике ресурсов виртуальной машины SQL в упрощенном режиме управляемого кода или без агента поддерживается только Azure PowerShellными API.

**Можно ли зарегистрировать виртуальную машину с помощью поставщика ресурсов виртуальной машины SQL перед установкой SQL Server?**

Нет. Для успешной регистрации в поставщике ресурсов виртуальной машины SQL виртуальная машина должна иметь хотя бы один экземпляр SQL Server. Если на виртуальной машине нет SQL Server экземпляра, новый ресурс micosoft. Склвиртуалмачине будет находиться в состоянии сбоя.

**Можно ли зарегистрировать виртуальную машину с поставщиком ресурсов виртуальной машины SQL, если имеется несколько SQL Server экземпляров?**

Да. Поставщик ресурсов виртуальной машины SQL будет регистрировать только один экземпляр SQL Server. Поставщик ресурсов виртуальной машины SQL будет регистрировать экземпляр SQL Server по умолчанию в случае нескольких экземпляров. Если экземпляр по умолчанию отсутствует, то поддерживается только регистрация в упрощенном режиме. Чтобы выполнить обновление с Lightweight на полный режим управления, должен существовать либо экземпляр SQL Server по умолчанию, либо виртуальная машина должна иметь только один именованный экземпляр SQL Server.

**Можно ли зарегистрировать экземпляр отказоустойчивого кластера SQL Server с помощью поставщика ресурсов виртуальной машины SQL?**

Да. SQL Server экземпляры отказоустойчивого кластера на виртуальной машине Azure можно зарегистрировать в поставщике ресурсов виртуальной машины SQL в упрощенном режиме. Однако SQL Server экземпляры отказоустойчивого кластера нельзя обновить до полного режима управления.

**Можно ли зарегистрировать виртуальную машину с поставщиком ресурсов виртуальной машины SQL, если настроена Always On группа доступности?**

Да. Регистрация SQL Server экземпляра на виртуальной машине Azure с помощью поставщика ресурсов виртуальной машины SQL не ограничена, если вы участвуете в конфигурации группы доступности Always On.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Часто задаваемые вопросы о SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценам для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
