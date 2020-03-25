---
title: Пример скрипта Azure PowerShell. Управление веб-трафиком | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Управление веб-трафиком с помощью шлюза приложений и масштабируемого набора виртуальных машин.
services: application-gateway
documentationcenter: networking
author: vhorne
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 89915cdd4ae88f1092c45e1d428679a99f1ac906
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76273405"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Управление веб-трафиком с помощью Azure PowerShell

С помощью этого скрипта создается шлюз приложений, в котором используется масштабируемый набор виртуальных машин для внутренних серверов. Затем шлюз приложений можно настроить для управления веб-трафиком. Выполнив скрипт, вы можете проверить шлюз приложений с помощью его общедоступного IP-адреса.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, шлюз приложений и все связанные ресурсы.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Создает конфигурацию подсети. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть с конфигурациями подсетей. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес для шлюза приложений. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Создает конфигурацию, которая связывает подсеть со шлюзом приложений. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Создает конфигурацию, которая назначает общедоступный IP-адрес шлюзу приложений. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Назначает порт для доступа к шлюзу приложений. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Создание серверного пула для шлюза приложений. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Настраивает параметры для серверного пула. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Создает прослушиватель. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Создает правило маршрутизации. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Установка уровня и производительности для шлюза приложений. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Создание шлюза приложений. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Создает профиль хранилища для масштабируемого набора. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | Определяет операционную систему для масштабируемого набора. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | Определяет сетевой интерфейс для масштабируемого набора. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | Создает масштабируемый набор виртуальных машин. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | Получает общедоступный IP-адрес шлюза приложений. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. | 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Другие примеры скриптов PowerShell можно найти в [документации по шлюзу приложений Azure](../powershell-samples.md).
