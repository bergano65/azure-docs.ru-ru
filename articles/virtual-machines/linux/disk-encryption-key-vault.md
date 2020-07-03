---
title: Создание и настройка хранилища ключей для шифрования дисков Azure
description: В этой статье описаны действия по созданию и настройке хранилища ключей для использования с шифрованием дисков Azure.
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0038d5fdb38fdcfd4130a710f51d764e0cf9d907
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459820"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Создание и настройка хранилища ключей для шифрования дисков Azure

Шифрование дисков Azure использует Azure Key Vault для управления ключами и секретами шифрования дисков, а так и управления ими.  Дополнительные сведения о хранилищах ключей см. в статье [Приступая к работе с Azure Key Vault](../../key-vault/key-vault-get-started.md) и [Защита хранилища ключей](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Если вы ранее использовали шифрование дисков Azure с Azure AD для шифрования виртуальной машины, необходимо продолжить использовать этот параметр для шифрования виртуальной машины. Дополнительные сведения см. [в статье Создание и Настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий выпуск)](disk-encryption-key-vault-aad.md) .

Создание и Настройка хранилища ключей для использования с шифрованием дисков Azure состоит из трех этапов:

1. При необходимости создайте группу ресурсов.
2. Создание хранилища ключей. 
3. Настройка политик расширенного доступа к хранилищу ключей.

Эти шаги показаны в следующих кратких руководствах:

- [Создание и шифрование виртуальной машины Linux с помощью Azure CLI](disk-encryption-cli-quickstart.md)
- [Создание и шифрование виртуальной машины Linux с помощью Azure PowerShell](disk-encryption-cli-quickstart.md)

Вы также можете создать или импортировать ключ шифрования ключа (KEK), если нужно.

> [!Note]
> Действия, описанные в этой статье, автоматизированы в [сценарии CLI для предварительных требований к шифрованию дисков Azure](https://github.com/ejarvi/ade-cli-getting-started) и [сценарии PowerShell для компонента шифрования дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Установка средств и подключение к Azure

Действия, описанные в этой статье, можно выполнить с помощью [Azure CLI](/cli/azure/), [модуля Azure PowerShell AZ](/powershell/azure/overview)или [портал Azure](https://portal.azure.com). 

Доступ к порталу возможен через браузер, Azure CLI и Azure PowerShell требуется локальная установка. Дополнительные сведения см. в [статье шифрование дисков Azure для Linux: Установка средств](disk-encryption-linux.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Перед использованием Azure CLI или Azure PowerShell необходимо сначала подключиться к подписке Azure. Для этого выполните [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Войдите в систему с помощью Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)или укажите учетные данные для портал Azure при появлении соответствующего запроса.

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
