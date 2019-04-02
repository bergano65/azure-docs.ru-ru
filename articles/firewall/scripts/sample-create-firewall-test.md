---
title: Пример сценария Azure PowerShell по созданию тестовой среды брандмауэра Azure
description: Пример скрипта Azure PowerShell для создания тестовой среды брандмауэра Azure.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: 6e85bd6ec51cff27fed6d0b2d9e73f94325e4d4f
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500242"
---
# <a name="create-an-azure-firewall-test-environment"></a>Создание тестовой среды брандмауэра Azure

Этот сценарий создает брандмауэр и тестовую сетевую среду. Сеть содержит одну виртуальную сеть с тремя подсетями: *AzureFirewallSubnet*, *ServersSubnet* и *JumpboxSubnet*. ServersSubnet и JumpboxSubnet содержат по одному 2-ядерному серверу Windows Server.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В AzureFirewallSubnet размещен брандмауэр, которому присваивается коллекция правил приложения, единственное правило которой разрешает доступ к www.microsoft.com.

Создается пользовательский маршрут, направляющий сетевой трафик из ServersSubnet через брандмауэр, где к нему применяются правила брандмауэра.

Скрипт можно выполнить из Azure [Cloud Shell](https://shell.azure.com/powershell) или из локальной установки PowerShell. 

При локальном запуске PowerShell для выполнения этого скрипта понадобится модуль Azure PowerShell. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. 

Если необходимо выполнить обновление, можно использовать модуль `PowerShellGet`, встроенный в Windows 10 и Windows Server 2016.

> [!NOTE]
>В других версиях Windows необходимо установить модуль `PowerShellGet` перед его использованием. Можно запустить команду `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path`, чтобы определить установлен ли этот модуль на компьютере. Если выходные данные не заполнены, необходимо установить последнюю версию [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Дополнительные сведения см. в статье об [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

Любая имеющаяся среда Azure PowerShell, установленная с помощью установщика веб-платформы, будет конфликтовать с установкой PowerShellGet, и ее нужно удалить.

Помните, что при локальном запуске PowerShell необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети и групп безопасности сети в этом скрипте используются приведенные ниже команды. Для каждой команды в следующей таблице приведены ссылки на соответствующую документацию:

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Создает объект конфигурации подсети. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть Azure и интерфейсную подсеть. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Создает правила безопасности, которые будут добавлены в группу безопасности сети. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Создает правила групп безопасности сети, которые разрешают или блокируют определенные порты для конкретных подсетей. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Связывает группы безопасности сети с подсетями. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес для доступа к виртуальной машине из Интернета. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Создает виртуальные сетевые интерфейсы и присоединяет их к интерфейсной и внутренней подсети виртуальной сети. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Создайте виртуальную машину. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |
|[New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)| Создает экземпляр службы "Брандмауэр Azure".|
|[Get-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/get-azfirewall)|Получает объект "Брандмауэр Azure".|
|[New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule)|Создает правило приложения службы "Брандмауэр Azure".|
|[Set-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/set-azfirewall)|Фиксирует изменения в объекте "Брандмауэр Azure".|

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

