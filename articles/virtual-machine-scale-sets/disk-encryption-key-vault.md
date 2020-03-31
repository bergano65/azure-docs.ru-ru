---
title: Создание и настройка хранилища ключей для шифрования дисков Azure
description: В этой статье представлены шаги по созданию и настройке хранилища ключей для использования с помощью шифрования azure Disk
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279014"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Создание и настройка хранилища ключей для шифрования дисков Azure

Шифрование azure Disk использует Azure Key Vault для управления ключами и секретами шифрования дисков и их управления.  Дополнительные сведения о хранилищах ключей см. в статье [Приступая к работе с Azure Key Vault](../key-vault/key-vault-get-started.md) и [Защита хранилища ключей](../key-vault/key-vault-secure-your-key-vault.md).

Создание и настройка хранилища ключей для использования с помощью шифрования azure Disk включает в себя три этапа:

1. Создание группы ресурсов, если это необходимо.
2. Создание хранилища ключей. 
3. Настройка политик расширенного доступа хранилища ключей.

Эти шаги иллюстрируются в следующих быстрых запусках:

Вы также можете, если хотите, создать или импортировать ключ шифрования (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Установка инструментов и подключение к Azure

Шаги в этой статье могут быть завершены с помощью модуля [Azure CLI,](/cli/azure/) [модуля Azure PowerShell Az](/powershell/azure/overview)или [портала Azure.](https://portal.azure.com)

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Прежде чем использовать Azure CLI или Azure PowerShell, необходимо сначала подключиться к подписке Azure. Вы делаете это, [восставляя с Azure CLI,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [вдаваясь в систему с Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)или поставляя свои учетные данные на портал Azure по запросу.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о шифровании дисков Azure](disk-encryption-overview.md)
- [Encrypt OS and attached data disks in a virtual machine scale set with the Azure CLI](disk-encryption-cli.md) (Шифрование ОС и подключенных дисков данных в масштабируемом наборе виртуальных машин с помощью Azure CLI)
- [Encrypt OS and attached data disks in a virtual machine scale set with Azure PowerShell](disk-encryption-powershell.md) (Шифрование ОС и подключенных дисков данных в масштабируемом наборе виртуальных машин с помощью Azure PowerShell)
