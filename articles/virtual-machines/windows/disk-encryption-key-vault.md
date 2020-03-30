---
title: Создание и настройка хранилища ключей для шифрования дисков Azure
description: В этой статье представлены шаги по созданию и настройке хранилища ключей для использования с помощью шифрования azure Disk
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72245244"
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

- [Создание и шифрование виртуальной машины Windows с помощью Azure CLI](disk-encryption-cli-quickstart.md)
- [Создание и шифрование виртуальной машины Windows с помощью Azure PowerShell](disk-encryption-cli-quickstart.md)

Вы также можете, если хотите, создать или импортировать ключ шифрования (KEK).

> [!Note]
> Шаги в этой статье автоматизированы в [предпосылках шифрования azure Disk CLI](https://github.com/ejarvi/ade-cli-getting-started) и [предпосылках шифрования azure Disk PowerShell.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Установка инструментов и подключение к Azure

Шаги в этой статье могут быть завершены с помощью модуля [Azure CLI,](/cli/azure/) [модуля Azure PowerShell Az](/powershell/azure/overview)или [портала Azure.](https://portal.azure.com)

В то время как портал доступен через ваш браузер, Azure CLI и Azure PowerShell требуют локальной установки; [см. Шифрование дисков Azure для Windows: Установка инструментов](disk-encryption-windows.md#install-tools-and-connect-to-azure) для получения подробной информации.

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Прежде чем использовать Azure CLI или Azure PowerShell, необходимо сначала подключиться к подписке Azure. Вы делаете это, [восставляя с Azure CLI,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [вдаваясь в систему с Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)или поставляя свои учетные данные на портал Azure по запросу.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Дальнейшие действия

- [Предпосылки для шифрования дисков Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Предпосылки для шифрования дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Изучите [сценарии шифрования дисков Azure на Windows VMs](disk-encryption-windows.md)
- Узнайте, как [устранить неполадки в шифровании azure Disk](disk-encryption-troubleshooting.md)
- Прочитайте [пример скриптов шифрования дисков Azure](disk-encryption-sample-scripts.md)
