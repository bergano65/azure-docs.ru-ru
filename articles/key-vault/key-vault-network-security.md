---
title: Настройка брандмауэров и виртуальных сетей Azure Key Vault — Azure Key Vault
description: Пошаговые инструкции для настройки брандмауэров и виртуальных сетей Key Vault.
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: a6f2e899e8be39abdefaf9d4f524eae457673c1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64694411"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Настройка брандмауэров и виртуальных сетей Azure Key Vault

В этой статье описываются пошаговые инструкции по настройке брандмауэров и виртуальных сетей Azure Key Vault для ограничения доступа к хранилищу ключей. [Конечные точки служб для виртуальной сети для Key Vault](key-vault-overview-vnet-service-endpoints.md) позволяют предоставить доступ для указанной виртуальной сети или набора диапазонов адресов IPv4 (протокол IP версии 4).

> [!IMPORTANT]
> Когда правила брандмауэра начнут действовать, пользователи смогут выполнять запросы на операции [плоскости данных](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) в Key Vault только из разрешенных виртуальных сетей или диапазонов IPv4-адресов. Это относится и к получению доступа к Key Vault с портала Azure. Пользователь сможет перейти в хранилище ключей с портала Azure, но не сможет получить список ключей, секретов и сертификатов, если клиентский компьютер не включен в список разрешенных. Это также влияет на выбор хранилища ключей другими службами Azure. Пользователи смогут просматривать список хранилищ ключей, но не список ключей, если правила брандмауэра запрещают их клиентские компьютеры.

## <a name="use-the-azure-portal"></a>Использование портала Azure

Брандмауэры и виртуальные сети Key Vault можно настроить с помощью портала Azure следующим образом:

1. Перейдите к хранилищу ключей, которое нужно защитить.
2. Выберите **Брандмауэры и виртуальные сети**.
3. В разделе **Разрешить доступ из** щелкните **Выбранные сети**.
4. Чтобы добавить имеющиеся виртуальные сети в правила брандмауэров и виртуальных сетей, выберите **+ Добавить существующие виртуальные сети**.
5. В новой открывшейся колонке выберите подписку, виртуальные сети и подсети, которым нужно предоставить доступ к этому хранилищу ключей. Если в выбранных вами виртуальных сетях и подсетях нет включенных конечных точек службы, подтвердите, что вы хотите включить конечные точки службы, и нажмите кнопку **Включить**. Эта настройка вступит в силу в течение 15 минут.
6. В разделе **IP-сети** добавьте диапазоны IPv4-адресов, введя их с использованием [нотации CIDR](https://tools.ietf.org/html/rfc4632), или укажите отдельные IP-адреса.
7. Щелкните **Сохранить**.

Вы также можете добавить новые виртуальные сети и подсети, а потом включить конечные точки службы для них, выбрав **+ Добавить новую виртуальную сеть**. Затем следуйте инструкциям на экране.

## <a name="use-the-azure-cli"></a>Использование Azure CLI 

Брандмауэры и виртуальные сети Key Vault можно настроить с помощью Azure CLI следующим образом:

1. [Установите Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и [войдите в систему](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Перечислите доступные правила виртуальной сети. Если вы не настроили каких-либо правил для этого хранилища ключей, список будет пуст.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Включите конечную точку службы для Key Vault в имеющейся виртуальной сети и подсети.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Добавьте сетевое правило для виртуальной сети и подсети.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Добавьте диапазон IP-адресов, из которого необходимо разрешать трафик.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Если это хранилище ключей должно быть доступно для каких-либо доверенных служб, задайте для параметра `bypass` значение `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Включите правила сети, установив для действия по умолчанию значение `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Брандмауэры и виртуальные сети Key Vault можно настроить с помощью PowerShell следующим образом:

1. Установите последнюю версию [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) и [выполните вход](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Перечислите доступные правила виртуальной сети. Если вы не настроили каких-либо правил для этого хранилища ключей, список будет пуст.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Включите конечную точку службы для Key Vault в имеющейся виртуальной сети и подсети.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Добавьте сетевое правило для виртуальной сети и подсети.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Добавьте диапазон IP-адресов, из которого необходимо разрешать трафик.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Если это хранилище ключей должно быть доступно для каких-либо доверенных служб, задайте для параметра `bypass` значение `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Включите правила сети, установив для действия по умолчанию значение `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Справочники

* Команда Azure CLI: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest).
* Командлеты Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet).

## <a name="next-steps"></a>Дальнейшие действия

* [Конечные точки служб для виртуальной сети для Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Защита хранилища ключей](key-vault-secure-your-key-vault.md)
