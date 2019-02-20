---
title: Важные особенности Управляемых дисков в Azure Stack и рекомендации по работе с ними | Документация Майкрософт
description: Дополнительные сведения о различиях и рекомендациях при работе с Управляемыми дисками в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 60f633049d6fdbb59744c8003e742ff649d48ea7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243399"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Управляемые диски Azure Stack. Отличия и рекомендации

В этой статье перечислены известные различия между [Управляемыми дисками Azure Stack](azure-stack-manage-vm-disks.md) и [Управляемыми дисками для Azure](../../virtual-machines/windows/managed-disks-overview.md). Чтобы узнать об общих различиях между Azure Stack и Azure, прочитайте статью [Важные аспекты использования служб и создания приложений в Azure Stack](azure-stack-considerations.md).

Эта служба упрощает управление дисками виртуальных машин IaaS. Они управляют [учетными записями хранения](../azure-stack-manage-storage-accounts.md), связанными с этими дисками.

> [!Note]  
> Управляемые диски в Azure Stack доступны, начиная с обновления 1808. Они теперь включены по умолчанию при создании виртуальных машин с помощью портала Azure Stack, начиная с обновления 1811.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Краткий справочник. Различия между Управляемыми дисками

| Функция | Azure (глобальная) | Azure Stack |
| --- | --- | --- |
|Шифрование неактивных данных |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE) (Шифрование службы хранилища Azure и шифрование дисков Azure)     |128-битное шифрование AES BitLocker      |
|Образ —          | Поддержка управляемого пользовательского образа |Поддерживаются|
|Варианты резервного копирования |Поддержка службы Azure Backup |Еще не поддерживается |
|Параметры аварийного восстановления |Поддержка Azure Site Recovery |Еще не поддерживается|
|Типы дисков     |SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") (Предварительная версия) и HDD (цен. категория "Стандартный") |SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") |
|Диски уровня "Премиум"  |Полностью поддерживается |Может быть подготовлено, но не имеет ограничений производительности или гарантий  |
|Операции ввода-вывода дисков уровня "Премиум"  |Зависит от размера диска  |2300 операций ввода-вывода в секунду на диск |
|Пропускная способность дисков уровня "Премиум" |Зависит от размера диска |145 МБ/с на диск |
|Размер диска  |Диски Azure ценовой категории "Премиум": от P4 (32 ГиБ) до P80 (32 ТиБ)<br>Диски SSD Azure ценовой категории "Стандартный": от E10 (128 ГиБ) до E80 (32 ТиБ)<br>Диски HDD Azure ценовой категории "Стандартный": от S4 (32 ГиБ) до S80 (32 ТиБ) |M4: 32 Гиб<br>M6: 64 Гиб<br>M10: 128 ГБ<br>M15: 256 Гиб<br>M20: 512 ГБ<br>M30: 1024 ГиБ |
|Моментальные снимки дисков|Поддерживается создание моментальных снимков управляемых дисков Azure, подключенных к запущенной виртуальной машине|Еще не поддерживается |
|Производительность аналитики дисков |Поддерживаются статистические и дисковые метрики |Еще не поддерживается |
|Миграция      |Предоставляется средство для переноса из существующих неуправляемых виртуальных машин Azure Resource Manager без необходимости повторного создания виртуальной машины  |Еще не поддерживается |

> [!NOTE]  
> Число операций ввода-вывода в Управляемых дисках и объем пропускной способности в Azure Stack задаются ограничениями, а не в ходе подготовки, и могут обуславливаться аппаратным обеспечением и рабочими нагрузками, выполняющимися в Azure Stack.

## <a name="metrics"></a>Метрики

Кроме того, есть различия в метриках хранилища:

- С помощью Azure Stack данные транзакций в метриках хранилища не различают внутреннюю или внешнюю пропускную способность сети.
- Данные транзакции Azure Stack в метриках хранилища не включают доступ виртуальных машин к подключенным дискам.

## <a name="api-versions"></a>Версии API

Управляемые диски Azure Stack поддерживает следующие версии API:

- 2017-03-30
- 2017-12-01

## <a name="managed-images"></a>Управляемые образы

Azure Stack поддерживает *управляемые образы*, которые позволяют создавать объект управляемого образа на универсальной виртуальной машине (как неуправляемой, так и управляемой), которая может создавать только виртуальные машины управляемого диска в будущем. Управляемые образы включают следующие два сценария.

- Вы обобщили неуправляемые виртуальные машины и хотите использовать управляемые диски в будущем.
- У вас есть универсальная управляемая виртуальная машина и вы хотите создать несколько похожих управляемых виртуальных машин.

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>Миграция неуправляемых виртуальных машин на управляемые диски

Следуйте инструкциям, описанным [здесь](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account), чтобы создать управляемый образ с помощью универсального виртуального жесткого диска в учетной записи хранения. Этот образ можно использовать для создания управляемых виртуальных машин, в дальнейшем.

### <a name="create-managed-image-from-vm"></a>Создание управляемого образа из виртуальной машины

После создания образа из существующей виртуальной машины управляемого диска используя инструкции, указанные [здесь](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell), с помощью следующего примера сценария создайте аналогичную виртуальную машину Linux из существующего объекта образа.

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Дополнительные сведения см. в статьях управляемого образа Azure [Создание управляемого образа универсальной виртуальной машины в Azure](../../virtual-machines/windows/capture-image-resource.md) и [Создание виртуальной машины из управляемого образа](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Параметр Configuration

После применения обновления 1808 или более поздней версии, необходимо выполнить следующую конфигурацию, прежде чем использовать Управляемые диски.

- Если подписка была создана до пакета обновления версии 1808, выполните следующие шаги, чтобы обновить подписку. В противном случае во время развертывания виртуальных машин для этой подписки может произойти сбой с сообщением об ошибке "Internal error in disk manager" (В диспетчере дисков произошла внутренняя ошибка).
   1. На портале клиента перейдите в раздел **Подписки** и найдите подписку. Щелкните **Поставщики ресурсов**, затем **Microsoft.Compute** и выберите **Повторная регистрация**.
   2. В той же подписке перейдите в **Управление доступом (IAM)** и убедитесь, что указан **Azure Stack — Управляемый диск**.
- Если вы используете среду с несколькими клиентами, попросите вашего оператора облака (из вашей организации или из компании поставщика служб) перенастроить каждый ваш гостевой каталог, выполнив шаги из [этой статьи](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). В противном случае, во время развертывания виртуальных машин в этой подписке, связанной с гостевым каталогом, может произойти сбой с сообщением об ошибке "В диспетчере дисков произошла внутренняя ошибка".

## <a name="next-steps"></a>Дополнительная информация

- [Сведения о виртуальных машинах Azure Stack](azure-stack-compute-overview.md)
