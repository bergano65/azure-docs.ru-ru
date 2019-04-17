---
title: Создание виртуальной машины SQL Server под управлением Windows с помощью Azure PowerShell | Документация Майкрософт
description: Это руководство содержит инструкции по созданию виртуальной машины SQL Server 2017 под управлением Windows с помощью Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c533f4671411412f223fb1c67f1b310ee19bcf23
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547559"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Краткое руководство. Создание виртуальной машины SQL Server под управлением Windows с помощью Azure PowerShell

В этом кратком руководстве перечислены основные действия по созданию виртуальной машины SQL Server с помощью Azure PowerShell.

> [!TIP]
> - В этом кратком руководстве приводятся инструкции по быстрой подготовке виртуальной машины SQL Server и подключению к ней. Дополнительные сведения о других способах создания виртуальных машин SQL Server с помощью Azure PowerShell см. в статье [Как создать виртуальные машины SQL Server с помощью Azure PowerShell](virtual-machines-windows-ps-sql-create.md).
> - Если у вас есть вопросы по виртуальным машинам SQL Server, см. раздел [часто задаваемых вопросов](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Оформление подписки Azure

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


## <a id="powershell"></a> Установка Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Настройка PowerShell

1. Откройте PowerShell и установите доступ к учетной записи Azure, выполнив команду **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Должно отобразиться окно для ввода учетных данных. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

1. Определите переменную с уникальным именем группы ресурсов. Для упрощения дальнейших действий в рамках этого руководства это имя будет использоваться в остальных командах в качестве основы для других имен ресурсов.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Определите расположение целевого региона Azure для всех ресурсов виртуальной машины.

   ```powershell
   $Location = "East US"
   ```

1. Создайте группу ресурсов.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Настройка параметров сети

1. Создайте виртуальную сеть, подсеть и общедоступный IP-адрес. Эти ресурсы используются для того, чтобы установить сетевое подключение к виртуальной машине и подключить ее к Интернету.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Создайте группу безопасности сети. Настройте правила, чтобы разрешить подключения к удаленному рабочему столу (RDP) и SQL Server.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Создайте сетевой интерфейс.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Создание виртуальной машины SQL

1. Определите учетные данные для входа на виртуальную машину. Используйте имя пользователя azureadmin. Перед выполнением команды убедитесь, что вы изменили \<password>.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Создайте объект конфигурации виртуальной машины, а затем создайте виртуальную машину. Следующая команда создает виртуальную машину SQL Server 2017 Developer Edition в ОС Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 | `
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Создание виртуальной машины занимает несколько минут.

## <a name="install-the-sql-iaas-agent"></a>Установка агента SQL IaaS

Для интеграции с порталом и получения компонентов виртуальной машины SQL необходимо установить [расширение агента SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Чтобы установить агент на новую виртуальную машину, после ее создания выполните следующую команду.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Подключение к виртуальной машине с помощью удаленного рабочего стола

1. Используйте следующую команду, чтобы получить общедоступный IP-адрес для новой виртуальной машины.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. После этого передайте возвращенный IP-адрес в качестве параметра командной строки для **mstsc**, чтобы запустить сеанс удаленного рабочего стола на новой виртуальной машине.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. При появлении запроса учетных данных выберите ввод учетных данных для другой учетной записи. Введите имя пользователя после обратной косой черты (например, `\azureadmin`) и пароль, заданные ранее в рамках этого краткого руководства.

## <a name="connect-to-sql-server"></a>Подключение к SQL Server

1. После входа в сеанс удаленного рабочего стола в меню "Пуск" запустите **SQL Server Management Studio 2017**.

1. В диалоговом окне **Подключение к серверу** оставьте значения по умолчанию. Имя сервера указано в качестве имени виртуальной машины. В параметрах проверки подлинности установлено значение **Проверка подлинности Windows**. Нажмите кнопку **Подключиться**.

Теперь вы подключены к серверу SQL Server локально. Если вы хотите подключиться удаленно, необходимо настроить подключение на портале или вручную. Дополнительные сведения об этом см. в [этой статье](virtual-machines-windows-sql-connect.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если постоянная работа виртуальной машины не требуется, вы можете избежать ненужных затрат, останавливая ее, когда она не используется. С помощью следующей команды можно остановить виртуальную машину, оставив ее доступной для использования в будущем.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

С помощью команды **Remove-AzResourceGroup** вы можете удалить все ресурсы, связанные с виртуальной машиной, без возможности восстановления. Это также приведет к окончательному удалению самой виртуальной машины, поэтому используйте указанную команду с осторожностью.

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы создали виртуальную машину SQL Server 2017 с помощью Azure PowerShell. Чтобы узнать больше о том, как перенести данные на новый сервер SQL Server, ознакомьтесь со следующей статьей.

> [!div class="nextstepaction"]
> [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)
