---
title: Создание и Настройка хранилища ключей для шифрования дисков Azure
description: В этой статье описаны действия по созданию и настройке хранилища ключей для использования с шифрованием дисков Azure.
ms.service: virtual-machine-scale-sets
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: e4f6dc068969efd3f66e6808531594ed4063347f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530879"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Создание и Настройка хранилища ключей для шифрования дисков Azure

Шифрование дисков Azure использует Azure Key Vault для управления ключами и секретами шифрования дисков, а так и управления ими.  Дополнительные сведения о хранилищах ключей см. в статье [Приступая к работе с Azure Key Vault](../key-vault/key-vault-get-started.md) и [Защита хранилища ключей](../key-vault/key-vault-secure-your-key-vault.md).

Создание и Настройка хранилища ключей для использования с шифрованием дисков Azure состоит из трех этапов:

1. При необходимости создайте группу ресурсов.
2. Создание хранилища ключей. 
3. Настройка политик расширенного доступа к хранилищу ключей.

Эти шаги показаны в следующих кратких руководствах:

Вы также можете создать или импортировать ключ шифрования ключа (KEK), если нужно.

## <a name="install-tools-and-connect-to-azure"></a>Установка средств и подключение к Azure

Действия, описанные в этой статье, можно выполнить с помощью [Azure CLI](/cli/azure/), [модуля Azure PowerShell AZ](/powershell/azure/overview)или [портал Azure](https://portal.azure.com).

Доступ к порталу возможен через браузер, Azure CLI и Azure PowerShell требуется локальная установка.

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Перед использованием Azure CLI или Azure PowerShell необходимо сначала подключиться к подписке Azure. Для этого выполните [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Войдите в систему с помощью Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)или укажите учетные данные для портал Azure при появлении соответствующего запроса.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о шифровании дисков Azure](disk-encryption-overview.md)
- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure CLI](disk-encryption-cli.md)
- [Шифрование масштабируемых наборов виртуальных машин с помощью Azure PowerShell](disk-encryption-powershell.md)