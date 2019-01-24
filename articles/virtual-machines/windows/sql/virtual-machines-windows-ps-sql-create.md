---
title: Руководство по подготовке виртуальных машин SQL Server с помощью Azure PowerShell | Документация Майкрософт
description: Содержит описание действий и команд PowerShell для создания виртуальной машины Azure на основе образа из коллекции образов виртуальных машин SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 60f04ac857079a1019ca744f3b26b0d05ae6ca6c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426936"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Как подготовить виртуальные машины SQL Server с помощью Azure PowerShell

В этом руководстве рассматриваются варианты создания виртуальных машин SQL Server под управлением ОС Windows с помощью Azure PowerShell. Краткий пример Azure PowerShell с дополнительными значениями по умолчанию приведен в статье [Создание виртуальной машины SQL Server под управлением Windows с помощью Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для работы с этой статьей требуется модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Настройка подписки

1. Откройте PowerShell и установите доступ к учетной записи Azure, выполнив команду **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Должно отобразиться окно для ввода учетных данных. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

## <a name="define-image-variables"></a>Определение переменных образа
Чтобы повторно использовать значения и упростить создание скриптов, начните с определения количества переменных. Изменяйте значения параметров по своему усмотрению, но учитывайте при этом ограничения именования, связанные с длиной имени и специальными знаками.

### <a name="location-and-resource-group"></a>Расположение и группа ресурсов
Определите область данных и группу ресурсов, в которых вы создадите другие ресурсы для виртуальной машины.

Измените, а затем выполните эти командлеты, чтобы инициализировать переменные.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Свойства хранилища
Определите учетную запись хранения и тип хранилища для виртуальной машины.

Измените, а затем выполните приведенный ниже командлет, чтобы инициализировать переменные. Для производственных рабочих нагрузок рекомендуется [хранилище класса Premium](../premium-storage.md).

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Свойства сети
Определите свойства, используемые для сети на виртуальной машине. 

- сетевому интерфейсу
- метод распределения TCP/IP;
- Имя виртуальной сети
- имя виртуальной подсети;
- диапазон IP-адресов для виртуальной сети;
- диапазон IP-адресов для подсети;
- Метка общедоступного доменного имени

Измените, а затем выполните эти командлеты, чтобы инициализировать переменные.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Свойства виртуальной машины
Определите имя виртуальной машины, имя компьютера, размер виртуальной машины и имя диска операционной системы для виртуальной машины.

Измените, а затем выполните эти командлеты, чтобы инициализировать переменные.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Выбор образа SQL Server

Используйте следующие переменные, чтобы определить образ SQL Server, который будет применен для виртуальной машины. 

1. Сначала получите список всех образов SQL Server, выполнив команду `Get-AzureRmVMImageOffer`. Эта команда выводит список текущих образов, доступных на портале Azure, и старых образов, которые устанавливаются только с помощью PowerShell:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. В данном руководстве для указания SQL Server 2017 в Windows Server 2016 используйте следующие переменные:

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Затем получите список выпусков, доступных для вашего предложения.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. В данном руководстве используется SQL Server 2017 Developer Edition (**SQLDEV**). Лицензию Developer Edition для тестирования и разработки можно получить бесплатно. Вы оплачиваете только стоимость выполнения виртуальной машины.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Если используется модель развертывания с помощью Resource Manager, первый создаваемый объект — группа ресурсов. Чтобы создать группу ресурсов и входящие в нее ресурсы Azure, используйте командлет [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Укажите переменные, инициализированные ранее для имени группы ресурсов и расположения.

Выполните следующий командлет, чтобы создать группу ресурсов.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения
Виртуальной машине требуются ресурсы хранения для диска операционной системы, а также файлов данных и журналов SQL Server. Для упрощения создайте один диск для всех ресурсов. Позже можно будет подключить дополнительные диски, выполнив командлет [Add-Azure Disk](/powershell/module/servicemanagement/azure/add-azuredisk), чтобы поместить файлы данных и журналов SQL Server на выделенные диски. Чтобы создать стандартную учетную запись хранения в новой группе ресурсов, используйте командлет [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Укажите переменные, инициализированные ранее для имени учетной записи хранения, SKU хранилища и расположения.

Выполните этот командлет, чтобы создать учетную запись хранения.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Создание учетной записи хранения может занять несколько минут.

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Виртуальной машине нужны сетевые ресурсы для подключения к сети.

* Каждой виртуальной машине требуется виртуальная сеть.
* Для виртуальной сети следует определить по крайней мере одну подсеть.
* Для сетевого интерфейса следует определить общедоступный или частный IP-адрес.

### <a name="create-a-virtual-network-subnet-configuration"></a>Создание конфигурации подсети в виртуальной сети
Сначала создайте конфигурацию подсети для виртуальной сети. В рамках этого руководства мы создадим подсеть по умолчанию, используя командлет [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Укажите переменные, инициализированные ранее для имени подсети и префикса адреса.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства конфигурации подсети виртуальной сети. Однако это выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать конфигурацию виртуальной подсети.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Далее с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) создайте виртуальную сеть в новой группе ресурсов. Укажите переменные, инициализированные ранее для имени, расположения и префикса адреса. Используйте конфигурацию подсети, определенную на предыдущем этапе.

Выполните следующий командлет, чтобы создать виртуальную сеть.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Создание общедоступного IP-адреса
Определив виртуальную сеть, необходимо настроить IP-адрес для подключения к виртуальной машине. В рамках этого руководства мы создадим общедоступный IP-адрес с применением динамического предоставления IP-адресов, чтобы обеспечить подключение к Интернету. Создайте общедоступный IP-адрес в новой группе ресурсов с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Укажите переменные, инициализированные ранее для имени, расположения, метода распределения и метки доменного имени DNS.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства общедоступного IP-адреса. Однако это выходит за рамки данного руководства. Кроме того, можно создать частный или статический адрес, но это также выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать общедоступный IP-адрес.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Создание группы безопасности сети
Чтобы защитить трафик виртуальной машины и SQL Server, создайте группу безопасности сети.

1. Сначала создайте правило группы безопасности сети для RDP, которое разрешает подключения с удаленного рабочего стола.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Настройте правило группы безопасности сети, которое разрешает трафик через TCP-порт 1433. Это позволит подключаться к SQL Server через Интернет.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Создайте группу безопасности сети.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Создание сетевого интерфейса
Теперь все готово для создания сетевого интерфейса, который будет использовать виртуальная машина. Создайте сетевой интерфейс в новой группе ресурсов с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Укажите определенные ранее имя, расположение, подсеть и общедоступный IP-адрес.

Выполните следующий командлет, чтобы создать сетевой интерфейс.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Настройка объекта виртуальной машины
После определения ресурсов хранения и сетевых ресурсов можно определить вычислительные ресурсы для виртуальной машины.

- Укажите размер виртуальной машины и различные свойства операционной системы.
- Укажите созданный ранее сетевой интерфейс.
- Определите хранилище BLOB-объектов.
- Укажите диск для операционной системы.

### <a name="create-the-vm-object"></a>Создание виртуальной машины
Сначала укажем размер виртуальной машины. В рамках этого руководства выберите размер DS13. Создайте настраиваемый объект виртуальной машины с помощью командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Укажите переменные, инициализированные ранее для имени и размера.

Выполните этот командлет, чтобы создать объект виртуальной машины.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Создание объекта учетных данных для хранения имени и пароля локального администратора
Чтобы задать свойства операционной системы для виртуальной машины, необходимо сначала предоставить учетные данные для учетной записи локального администратора в виде защищенной строки. Для этого используйте командлет [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Выполните следующий командлет и в окне запроса учетных данных PowerShell введите имя и пароль, которые будут использоваться для учетной записи локального администратора на виртуальной машине.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Определение свойств операционной системы для виртуальной машины
Теперь можно настроить свойства операционной системы виртуальной машины, выполнив командлет [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem).

- Укажите Windows в качестве типа операционной системы.
- Настройте обязательную установку [агента виртуальной машины](../../extensions/agent-windows.md).
- Укажите, что командлет включает автоматическое обновление.
- Укажите инициализированные ранее переменные для имени виртуальной машины, имени компьютера и учетных данных.

Выполните следующий командлет, чтобы задать свойства операционной системы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Добавление сетевого интерфейса к виртуальной машине
Используйте командлет [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) и определенную ранее переменную, чтобы добавить сетевой интерфейс.

Выполните следующий командлет, чтобы указать сетевой интерфейс для виртуальной машины.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Определение расположения хранилища BLOB-объектов для диска, используемого виртуальной машиной
Далее укажите расположение хранилища BLOB-объектов для диска виртуальной машины с помощью определенных ранее переменных.

Выполните следующий командлет, чтобы задать расположение хранилища BLOB-объектов.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Определение свойств диска операционной системы для виртуальной машины
Затем определите свойства диска операционной системы для виртуальной машины с помощью командлета [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). 

- Укажите для виртуальной машины операционную систему, которая будет запускаться из образа.
- Для кэширования установите параметр "только для чтения" (так как SQL Server установлен на этом же диске).
- Укажите переменные, инициализированные ранее для имени виртуальной машины и диска операционной системы.

Выполните следующий командлет, чтобы задать свойства диска операционной системы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Определение образа платформы для виртуальной машины
Последний этап конфигурации — определение образа платформы для виртуальной машины. В рамках этого руководства используется последний образ SQL Server 2016 CTP-версии. Чтобы задать образ согласно значениям переменных, определенных ранее, используйте командлет [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage).

Выполните следующий командлет, чтобы указать образ платформы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Создание виртуальной машины SQL
Теперь, по завершении конфигурации, все готово для создания виртуальной машины. Для этого используйте командлет [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) и уже определенные переменные.

> [!TIP]
> Создание виртуальной машины может занять несколько минут.

Выполните следующий командлет, чтобы создать виртуальную машину.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Теперь виртуальная машина создана.

> [!NOTE]
> Если вы получите сообщение об ошибке диагностики загрузки, ее можно пропустить. Стандартная учетная запись хранения создается для диагностики загрузки, так как для диска виртуальной машины указана учетная запись хранилища класса Premium.

## <a name="install-the-sql-iaas-agent"></a>Установка агента SQL IaaS
Виртуальные машины SQL Server поддерживают функции автоматизированного управления при наличии [расширения агента IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Чтобы установить агент на новой виртуальной машине, после его создания выполните следующую команду.


   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>Останов или удаление виртуальной машины

Если не требуется, чтобы виртуальная машина работала постоянно, можно избежать ненужных затрат, останавливая ее, когда она не используется. С помощью следующей команды можно остановить виртуальную машину, оставив ее доступной для использования в будущем.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

А с помощью команды **Remove-AzureRmResourceGroup** можно удалить все ресурсы, связанные с виртуальной машиной, без возможности восстановления. Это также приведет к окончательному удалению самой виртуальной машины, поэтому указанную команду следует использовать с осторожностью.

## <a name="example-script"></a>Пример сценария
Файл ниже содержит полный сценарий PowerShell для этого руководства. Предполагается, что вы уже настроили подписку Azure для использования с командами **Connect-AzureRmAccount** и **Select-AzureRmSubscription**.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Дополнительная информация
После создания виртуальной машины можно:

- подключаться к виртуальной машине с помощью протокола удаленного рабочего стола (RDP);
- Настраивать параметры SQL Server на портале для виртуальной машины, включая:
   - [параметры хранилища](virtual-machines-windows-sql-server-storage-configuration.md); 
   - [автоматизированные задачи управления](virtual-machines-windows-sql-server-agent-extension.md).
- [настраивать подключение](virtual-machines-windows-sql-connect.md);
- подключать клиентов и приложения к новому экземпляру SQL Server.

