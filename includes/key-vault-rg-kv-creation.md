---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87512820"
---
В этом кратком руководстве используется предварительно созданное хранилище ключей Azure. Хранилище ключей можно создать, выполнив действия, описанные в кратких руководствах по [Azure CLI](/azure/key-vault/general/quick-create-cli), [Azure PowerShell](/azure/key-vault/general/quick-create-powershell) или [порталу Azure](/azure/key-vault/general/quick-create-portal). 

Можно также выполнить команды Azure CLI или Azure PowerShell, приведенные ниже.

> [!Important]
> Каждое хранилище ключей должно иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```