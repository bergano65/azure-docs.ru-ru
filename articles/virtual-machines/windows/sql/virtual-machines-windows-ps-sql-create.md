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
ms.openlocfilehash: edf5f2b681123243f55b1c2bf19a500e68171c0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66165734"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Как подготовить виртуальные машины SQL Server с помощью Azure PowerShell

В этом руководстве рассматриваются варианты создания виртуальных машин SQL Server под управлением ОС Windows с помощью Azure PowerShell. Краткий пример Azure PowerShell с дополнительными значениями по умолчанию приведен в статье [Создание виртуальной машины SQL Server под управлением Windows с помощью Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Настройка подписки

1. Откройте PowerShell и установите доступ к учетной записи Azure, выполнив команду **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Должно отобразиться окно для ввода учетных данных. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

## <a name="define-image-variables"></a>Определение переменных образа
Чтобы повторно использовать значения и упростить создание скриптов, начните с определения количества переменных. Изменяйте значения параметров по своему усмотрению, но учитывайте при этом ограничения именования, связанные с длиной имени и специальными знаками.

### <a name="location-and-resource-group"></a>Расположение и группа ресурсов
Определите область данных и группу ресурсов, в которых вы создадите другие ресурсы для виртуальной машины.

Измените, а затем выполните эти командлеты, чтобы инициализировать переменные.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Свойства хранилища
Определите учетную запись хранения и тип хранилища для виртуальной машины.

Измените, а затем выполните приведенный ниже командлет, чтобы инициализировать переменные. Для производственных рабочих нагрузок рекомендуются [SSD (цен. категория "Премиум")](../disks-types.md#premium-ssd).

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Свойства сети
Определите свойства, используемые для сети на виртуальной машине. 

- Сетевой интерфейс
- метод распределения TCP/IP;
- Имя виртуальной сети
- имя виртуальной подсети;
- диапазон IP-адресов для виртуальной сети;
- диапазон IP-адресов для подсети;
- Метка общедоступного доменного имени

Измените, а затем выполните эти командлеты, чтобы инициализировать переменные.

```powershell
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

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Выбор образа SQL Server

Используйте следующие переменные, чтобы определить образ SQL Server, который будет применен для виртуальной машины. 

1. Сначала получите список всех образов SQL Server, выполнив команду `Get-AzVMImageOffer`. Эта команда выводит список текущих образов, доступных на портале Azure, и старых образов, которые устанавливаются только с помощью PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. В данном руководстве для указания SQL Server 2017 в Windows Server 2016 используйте следующие переменные:

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Затем получите список выпусков, доступных для вашего предложения.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. В данном руководстве используется SQL Server 2017 Developer Edition (**SQLDEV**). Лицензию Developer Edition для тестирования и разработки можно получить бесплатно. Вы оплачиваете только стоимость выполнения виртуальной машины.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Если используется модель развертывания с помощью Resource Manager, первый создаваемый объект — группа ресурсов. Чтобы создать группу ресурсов и входящие в нее ресурсы Azure, используйте командлет [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Укажите переменные, инициализированные ранее для имени группы ресурсов и расположения.

Выполните следующий командлет, чтобы создать группу ресурсов.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения
Виртуальной машине требуются ресурсы хранения для диска операционной системы, а также файлов данных и журналов SQL Server. Для упрощения создайте один диск для всех ресурсов. Позже можно будет подключить дополнительные диски, выполнив командлет [Add-Azure Disk](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk), чтобы поместить файлы данных и журналов SQL Server на выделенные диски. Чтобы создать стандартную учетную запись хранения в новой группе ресурсов, используйте командлет [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount). Укажите переменные, инициализированные ранее для имени учетной записи хранения, SKU хранилища и расположения.

Выполните этот командлет, чтобы создать учетную запись хранения.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
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
Сначала создайте конфигурацию подсети для виртуальной сети. В рамках этого руководства мы создадим подсеть по умолчанию, используя командлет [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Укажите переменные, инициализированные ранее для имени подсети и префикса адреса.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства конфигурации подсети виртуальной сети. Однако это выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать конфигурацию виртуальной подсети.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Далее с помощью командлета [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) создайте виртуальную сеть в новой группе ресурсов. Укажите переменные, инициализированные ранее для имени, расположения и префикса адреса. Используйте конфигурацию подсети, определенную на предыдущем этапе.

Выполните следующий командлет, чтобы создать виртуальную сеть.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Создание общедоступного IP-адреса
Определив виртуальную сеть, необходимо настроить IP-адрес для подключения к виртуальной машине. В рамках этого руководства мы создадим общедоступный IP-адрес с применением динамического предоставления IP-адресов, чтобы обеспечить подключение к Интернету. Создайте общедоступный IP-адрес в новой группе ресурсов с помощью командлета [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress). Укажите переменные, инициализированные ранее для имени, расположения, метода распределения и метки доменного имени DNS.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства общедоступного IP-адреса. Однако это выходит за рамки данного руководства. Кроме того, можно создать частный или статический адрес, но это также выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать общедоступный IP-адрес.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Создание группы безопасности сети
Чтобы защитить трафик виртуальной машины и SQL Server, создайте группу безопасности сети.

1. Сначала создайте правило группы безопасности сети для RDP, которое разрешает подключения с удаленного рабочего стола.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Настройте правило группы безопасности сети, которое разрешает трафик через TCP-порт 1433. Это позволит подключаться к SQL Server через Интернет.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Создайте группу безопасности сети.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Создание сетевого интерфейса
Теперь все готово для создания сетевого интерфейса, который будет использовать виртуальная машина. Создайте сетевой интерфейс в новой группе ресурсов с помощью командлета [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Укажите определенные ранее имя, расположение, подсеть и общедоступный IP-адрес.

Выполните следующий командлет, чтобы создать сетевой интерфейс.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
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
Сначала укажем размер виртуальной машины. В рамках этого руководства выберите размер DS13. Создайте настраиваемый объект виртуальной машины с помощью командлета [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). Укажите переменные, инициализированные ранее для имени и размера.

Выполните этот командлет, чтобы создать объект виртуальной машины.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Создание объекта учетных данных для хранения имени и пароля локального администратора
Чтобы задать свойства операционной системы для виртуальной машины, необходимо сначала предоставить учетные данные для учетной записи локального администратора в виде защищенной строки. Для этого используйте командлет [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Выполните следующий командлет и в окне запроса учетных данных PowerShell введите имя и пароль, которые будут использоваться для учетной записи локального администратора на виртуальной машине.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Определение свойств операционной системы для виртуальной машины
Теперь вы можете настроить свойства операционной системы виртуальной машины, выполнив командлет [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem).

- Укажите Windows в качестве типа операционной системы.
- Настройте обязательную установку [агента виртуальной машины](../../extensions/agent-windows.md).
- Укажите, что командлет включает автоматическое обновление.
- Укажите инициализированные ранее переменные для имени виртуальной машины, имени компьютера и учетных данных.

Выполните следующий командлет, чтобы задать свойства операционной системы для виртуальной машины.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Добавление сетевого интерфейса к виртуальной машине
Используйте командлет [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) и определенную ранее переменную, чтобы добавить сетевой интерфейс.

Выполните следующий командлет, чтобы указать сетевой интерфейс для виртуальной машины.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Определение расположения хранилища BLOB-объектов для диска, используемого виртуальной машиной
Далее укажите расположение хранилища BLOB-объектов для диска виртуальной машины с помощью определенных ранее переменных.

Выполните следующий командлет, чтобы задать расположение хранилища BLOB-объектов.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Определение свойств диска операционной системы для виртуальной машины
Затем определите свойства диска операционной системы для виртуальной машины с помощью командлета [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). 

- Укажите для виртуальной машины операционную систему, которая будет запускаться из образа.
- Для кэширования установите параметр "только для чтения" (так как SQL Server установлен на этом же диске).
- Укажите переменные, инициализированные ранее для имени виртуальной машины и диска операционной системы.

Выполните следующий командлет, чтобы задать свойства диска операционной системы для виртуальной машины.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Определение образа платформы для виртуальной машины
Последний этап конфигурации — определение образа платформы для виртуальной машины. В рамках этого руководства используется последний образ SQL Server 2016 CTP-версии. Чтобы использовать образ согласно значениям переменных, определенных ранее, примените командлет [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage).

Выполните следующий командлет, чтобы указать образ платформы для виртуальной машины.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Создание виртуальной машины SQL
Теперь, по завершении конфигурации, все готово для создания виртуальной машины. Для этого используйте командлет [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) и уже определенные переменные.

> [!TIP]
> Создание виртуальной машины может занять несколько минут.

Выполните следующий командлет, чтобы создать виртуальную машину.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Теперь виртуальная машина создана.

> [!NOTE]
> Если вы получите сообщение об ошибке диагностики загрузки, ее можно пропустить. Стандартная учетная запись хранения создается для диагностики загрузки, так как для диска виртуальной машины указана учетная запись хранилища класса Premium.

## <a name="install-the-sql-iaas-agent"></a>Установка агента SQL IaaS
Виртуальные машины SQL Server поддерживают функции автоматизированного управления при наличии [расширения агента IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Чтобы установить агент на новой виртуальной машине, после его создания выполните следующую команду.


   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>Останов или удаление виртуальной машины

Если не требуется, чтобы виртуальная машина работала постоянно, можно избежать ненужных затрат, останавливая ее, когда она не используется. С помощью следующей команды можно остановить виртуальную машину, оставив ее доступной для использования в будущем.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

С помощью команды **Remove-AzResourceGroup** вы можете удалить все ресурсы, связанные с виртуальной машиной, без возможности восстановления. Это также приведет к окончательному удалению самой виртуальной машины, поэтому используйте указанную команду с осторожностью.

## <a name="example-script"></a>Пример сценария
Файл ниже содержит полный сценарий PowerShell для этого руководства. Предполагается, что вы уже настроили подписку Azure для использования с командами **Connect-AzAccount** и **Select-AzSubscription**.

```powershell
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
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Дальнейшие действия
После создания виртуальной машины можно:

- подключаться к виртуальной машине с помощью протокола удаленного рабочего стола (RDP);
- Настраивать параметры SQL Server на портале для виртуальной машины, включая:
   - [параметры хранилища](virtual-machines-windows-sql-server-storage-configuration.md); 
   - [автоматизированные задачи управления](virtual-machines-windows-sql-server-agent-extension.md).
- [настраивать подключение](virtual-machines-windows-sql-connect.md);
- подключать клиентов и приложения к новому экземпляру SQL Server.

