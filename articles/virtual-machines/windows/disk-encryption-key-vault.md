---
title: Создание и Настройка хранилища ключей для шифрования дисков Azure
description: В этой статье описаны действия по созданию и настройке хранилища ключей для использования с шифрованием дисков Azure.
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245244"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Создание и Настройка хранилища ключей для шифрования дисков Azure

Шифрование дисков Azure использует Azure Key Vault для управления ключами и секретами шифрования дисков, а так и управления ими.  Дополнительные сведения о хранилищах ключей см. в статье [Приступая к работе с Azure Key Vault](../../key-vault/key-vault-get-started.md) и [Защита хранилища ключей](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Если вы ранее использовали шифрование дисков Azure с Azure AD для шифрования виртуальной машины, необходимо продолжить использовать этот параметр для шифрования виртуальной машины. Дополнительные сведения см. [в статье Создание и Настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий выпуск)](disk-encryption-key-vault-aad.md) .

Создание и Настройка хранилища ключей для использования с шифрованием дисков Azure состоит из трех этапов:

1. При необходимости создайте группу ресурсов.
2. Создание хранилища ключей. 
3. Настройка политик расширенного доступа к хранилищу ключей.

Эти шаги показаны в следующих кратких руководствах:

- [Создание и шифрование виртуальной машины Windows с помощью Azure CLI](disk-encryption-cli-quickstart.md)
- [Создание и шифрование виртуальной машины Windows с помощью Azure PowerShell](disk-encryption-cli-quickstart.md)

Вы также можете создать или импортировать ключ шифрования ключа (KEK), если нужно.

> [!Note]
> Действия, описанные в этой статье, автоматизированы в [сценарии CLI для предварительных требований к шифрованию дисков Azure](https://github.com/ejarvi/ade-cli-getting-started) и [сценарии PowerShell для компонента шифрования дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Установка средств и подключение к Azure

Действия, описанные в этой статье, можно выполнить с помощью [Azure CLI](/cli/azure/), [модуля Azure PowerShell AZ](/powershell/azure/overview)или [портал Azure](https://portal.azure.com).

Доступ к порталу возможен через браузер, Azure CLI и Azure PowerShell требуется локальная установка. Дополнительные сведения см. в [статье шифрование дисков Azure для Windows: Установка средств](disk-encryption-windows.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Перед использованием Azure CLI или Azure PowerShell необходимо сначала подключиться к подписке Azure. Для этого выполните [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Войдите в систему с помощью Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)или укажите учетные данные для портал Azure при появлении соответствующего запроса.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Дополнительная информация

- [Сценарий CLI для предварительных требований к шифрованию дисков Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Сценарий PowerShell для предварительных требований к шифрованию дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Сведения о [сценариях шифрования дисков Azure на виртуальных машинах Windows](disk-encryption-windows.md)
- Сведения об [устранении неполадок шифрования дисков Azure](disk-encryption-troubleshooting.md)
- Ознакомьтесь с [примерами сценариев шифрования дисков Azure](disk-encryption-sample-scripts.md) .
