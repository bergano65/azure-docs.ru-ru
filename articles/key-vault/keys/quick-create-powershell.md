---
title: Создание и получение атрибутов ключа в Azure Key Vault с помощью Azure PowerShell
description: Краткое руководство по настройке и получению ключа из Azure Key Vault с помощью Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 3cea28fa278bb699a7fb5aaa0c79bf1e71f59ac1
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940503"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Краткое руководство. Настройка и получение ключа из Azure Key Vault с помощью Azure PowerShell

Из этого краткого руководства вы узнаете, как создать хранилище ключей в Azure Key Vault с помощью Azure PowerShell. Azure Key Vault — это облачная служба, которая работает как защищенное хранилище секретов. Вы можете безопасно хранить ключи, пароли, сертификаты и другие секреты. Дополнительные сведения о хранилище ключей см. в статье [обзора](../general/overview.md). Azure PowerShell используется для создания ресурсов Azure и управления ими с помощью скриптов и команд. Создав ресурс, вы сможете сохранить в нем ключ.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, понадобится модуль Azure PowerShell 1.0.0 или более поздней версии. Чтобы узнать версию, введите `$PSVersionTable.PSVersion`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzAccount`, чтобы создать подключение к Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Создание хранилища ключей

Далее вы создадите Key Vault. При выполнении этого шага вам понадобятся некоторые сведения:

Хотя мы используем Contoso KeyVault2 как имя для Key Vault в рамках работы с этим руководством, вы должны использовать уникальное имя.

- **Имя хранилища**: Contoso-Vault2.
- **Имя группы ресурсов**: ContosoResourceGroup.
- **Расположение**: восточная часть США.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

В выходных данных командлета будут показаны свойства созданного Key Vault. Запишите значения двух указанных ниже свойств.

* **Имя хранилища.** В этом примере используется имя **Contoso-Vault2**. Вы будете использовать это имя для выполнения других командлетов хранилища ключей;
* **URI хранилища**. В нашем примере это https://Contoso-Vault2.vault.azure.net/. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

После создания хранилища ваша учетная запись Azure будет единственной учетной записью, с которой можно выполнять любые действия в новом хранилище.

## <a name="add-a-key-to-key-vault"></a>Добавление ключа в Key Vault

Чтобы добавить ключ в хранилище, вам нужно выполнить несколько дополнительных действий. Этот ключ может использоваться приложением. 

Выполните приведенные ниже команды, чтобы создать вызываемый ключ **ExampleKey**:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

Теперь ключ, добавленный в Azure Key Vault, можно вызвать, используя его URI. Используйте **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** , чтобы получить текущую версию. 

Чтобы просмотреть ранее сохраненный ключ, выполните следующие действия:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Вы создали Key Vault, сохранили в нем ключ и извлекли его.

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие руководства в этой серии созданы на основе этого документа. Если вы планируете продолжить работу с последующими краткими руководствами и статьями, эти ресурсы можно не удалять.
Чтобы удалить группу ресурсов и все связанные с ней ресурсы, выполните команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Удалите ресурсы следующим образом:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как создать Key Vault и сохранить в нем сертификат. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](../general/overview.md)
- Справочник по [командлетам Azure PowerShell для Azure Key Vault](/powershell/module/az.keyvault/)
- [Рекомендации по Azure Key Vault](../general/best-practices.md)
