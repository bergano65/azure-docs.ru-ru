---
title: Создание и настройка хранилища ключей для шифрования дисков Azure
description: В этой статье представлены шаги по созданию и настройке хранилища ключей для использования с помощью шифрования azure Disk
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5d9acb525f35da756a986826574082f1ecafedf5
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314117"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Создание и настройка хранилища ключей для шифрования дисков Azure

Шифрование azure Disk использует Azure Key Vault для управления ключами и секретами шифрования дисков и их управления.  Дополнительные сведения о хранилищах ключей см. в статье [Приступая к работе с Azure Key Vault](../../key-vault/key-vault-get-started.md) и [Защита хранилища ключей](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Если ранее для шифрования VM использовалось шифрование Azure Disk с Azure AD, необходимо продолжить использование этой опции для шифрования VM. [См. Создание и настройку хранилища ключей для шифрования azure Disk с Azure AD (предыдущий релиз)](disk-encryption-key-vault-aad.md) для получения подробной информации.

Создание и настройка хранилища ключей для использования с помощью шифрования azure Disk включает в себя три этапа:

1. Создание группы ресурсов, если это необходимо.
2. Создание хранилища ключей. 
3. Настройка политик расширенного доступа хранилища ключей.

Эти шаги иллюстрируются в следующих быстрых запусках:

- [Создание и шифрование виртуальной машины Linux с помощью Azure CLI](disk-encryption-cli-quickstart.md)
- [Создание и шифрование Linux VM с помощью Azure PowerShell](disk-encryption-cli-quickstart.md)

Вы также можете, если хотите, создать или импортировать ключ шифрования (KEK).

> [!Note]
> Шаги в этой статье автоматизированы в [предпосылках шифрования azure Disk CLI](https://github.com/ejarvi/ade-cli-getting-started) и [предпосылках шифрования azure Disk PowerShell.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Установка инструментов и подключение к Azure

Шаги в этой статье могут быть завершены с помощью модуля [Azure CLI,](/cli/azure/) [модуля Azure PowerShell Az](/powershell/azure/overview)или [портала Azure.](https://portal.azure.com) 

В то время как портал доступен через ваш браузер, Azure CLI и Azure PowerShell требуют локальной установки; [см. Шифрование дисков Azure для Linux: Установка инструментов](disk-encryption-linux.md#install-tools-and-connect-to-azure) для получения подробной информации.

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Прежде чем использовать Azure CLI или Azure PowerShell, необходимо сначала подключиться к подписке Azure. Вы делаете это, [восставляя с Azure CLI,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [вдаваясь в систему с Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)или поставляя свои учетные данные на портал Azure по запросу.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
