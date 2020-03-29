---
title: Создание и настройка хранилища ключей для шифрования дисков Azure
description: В этой статье представлены шаги по созданию и настройке хранилища ключей для использования с помощью шифрования azure Disk
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970629"
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

## <a name="create-a-resource-group"></a>Создание группы ресурсов

*Если у вас уже есть группа ресурсов, можно перейти к [созданию хранилища ключей.](#create-a-key-vault)*

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

Создайте группу ресурсов с помощью [группы az, создайте](/cli/azure/group?view=azure-cli-latest#az-group-create) команду Azure CLI, команду [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell или с [портала Azure.](https://portal.azure.com)

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

*Если у вас уже есть хранилище ключей, вы можете перейти к [настройке политик расширенного доступа хранилища ключей.](#set-key-vault-advanced-access-policies)*

Создайте хранилище ключей, используя команду [Az keyvault,](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) команду Azure CLI, команду [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell, [портал Azure](https://portal.azure.com)или [шаблон менеджера ресурсов.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

>[!WARNING]
> Для обеспечения того, чтобы секреты шифрования не пересекали региональные границы, Шифрование дисков Azure требует, чтобы Key Vault и VMs были расположены в одном регионе. Создайте и используйте Key Vault, который находится в том же регионе, что и вшивки в использовании ввода-нора. 

Каждый Key Vault должен иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

### <a name="azure-cli"></a>Azure CLI

При создании хранилища ключей с помощью Azure CLI добавьте флаг "--включен для шифрования дисков".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

При создании хранилища ключей с помощью Azure PowerShell добавьте флаг «-EnabledForDiskEncryption».

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Шаблон Resource Manager

Вы также можете создать хранилище ключей с помощью [шаблона Resource Manager.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).
2. Выберите подписку, группу ресурсов, местоположение группы ресурсов, имя Key Vault, идентификатор объекта, юридические условия и соглашение, а затем нажмите **«Покупка».** 


##  <a name="set-key-vault-advanced-access-policies"></a>установить политики расширенного доступа к хранилищу ключей.

Платформа Azure должна иметь доступ к ключам шифрования или секретам, расположенным в вашем хранилище ключей, чтобы предоставлять их виртуальной машине при ее загрузке для расшифровки томов. 

Если вы не включили хранилище ключей для шифрования, развертывания или развертывания шаблонов на момент создания (как это было продемонстрировано на предыдущем этапе), необходимо обновить политику расширенного доступа.  

### <a name="azure-cli"></a>Azure CLI

Включите шифрование дисков для хранилища ключей с помощью команды [az keyvault update](/cli/azure/keyvault#az-keyvault-update). 

 - **Включение хранилища ключей для шифрования диска:** требуется использовать параметр enabled-for-disk-encryption. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Включение хранилища ключей для развертывания (если необходимо):** позволяет поставщику ресурсов Microsoft.Compute извлекать секреты из этого хранилища ключей в случае использования ссылки на него при создании ресурса, например, виртуальной машины.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Включение хранилища ключей для развертывания шаблона (если необходимо):** позволяет Resource Manager получать секреты из хранилища.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Используйте хранилище ключей PowerShell cmdlet [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) чтобы включить шифрование диска для хранилища ключей.

  - **Включение хранилища ключей для шифрования диска:** для шифрования дисков Azure требуется использовать параметр EnabledForDiskEncryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Включение хранилища ключей для развертывания (если необходимо):** позволяет поставщику ресурсов Microsoft.Compute извлекать секреты из этого хранилища ключей в случае использования ссылки на него при создании ресурса, например, виртуальной машины.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Включение хранилища ключей для развертывания шаблона (если необходимо):** позволяет Azure Resource Manager получать секреты из этого хранилища ключей в случае использования ссылки на него при развертывании шаблона.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Портал Azure

1. Выберите хранилище ключей, перейдите на **политику доступа**и **нажмите кнопку, чтобы показать расширенные политики доступа.**
2. Установите флажок **Включить доступ к шифрованию дисков Azure для шифрования томов**.
3. Выберите **Включить доступ к виртуальным машинам Azure для развертывания** или **Включить доступ к Azure Resource Manager для развертывания шаблонов**, если это необходимо. 
4. Нажмите **Сохранить**.

    ![Установка политики расширенного доступа к хранилищу Azure Key Vault](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Настройка ключа шифрования (KEK)

Если вы хотите использовать ключ шифрования ключей (KEK), чтобы добавить уровень безопасности ключей шифрования, поместите KEK в хранилище ключей. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault.

Вы можете создать новый KEK с помощью клавиатуры Azure CLI [az keyvault,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) команды Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet или [портала Azure.](https://portal.azure.com/) Необходимо создать тип ключа RSA; Шифрование azure Disk пока не поддерживает использование ключей Elliptic Curve.

Вместо этого вы можете импортировать KEK из вашего собственного ключевого управления HSM. Дополнительные сведения см. в документации по [Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). 

Ваши ключевые URL-адреса хранилища KEK должны быть versioned. Это требование Azure. Ниже приведены примеры действительных URL-адресов секрета и ключа шифрования ключей.

* Пример действительного секретного URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Пример действительного URL-адреса KEK:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Шифрование дисков Azure не поддерживает указание номеров портов в URL-адресах секрета и ключа шифрования ключей для хранилища ключей. Ниже приведены примеры недопустимых и допустимых URL-адресов хранилища ключей.

  * Приемлемый URL-адрес хранилища ключей:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Неприемлемый URL-адрес хранилища ключей:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Используйте [клавиатуру keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) Az Az, создавая команду для создания нового KEK и его хранения в хранилище ключей.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Вместо этого можно импортировать закрытый ключ с помощью команды [импорта ключей Keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) Az:

В любом случае вы будете поставлять имя вашего KEK в шифрование Azure CLI [az vm, позволяющее](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --ключевое шифрование-ключ параметром. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Используйте смдlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) для создания нового KEK и его хранения в хранилище ключей.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Вместо этого можно импортировать закрытый ключ, используя команду [импорта ключей Keyvault Azvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) Az.

В любом случае, вы будете поставлять идентификатор вашего ключа KEK Vault и URL-адрес вашего KEK в Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId и -KeyEncryptionKeyUrl параметров. Обратите внимание, что этот пример предполагает, что вы используете один и тот же ключ свода как для ключа шифрования диска, так и для KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Дальнейшие действия

- [Предпосылки для шифрования дисков Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Предпосылки для шифрования дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Изучите [сценарии шифрования дисков Azure на Linux VMs](disk-encryption-linux.md)
- Узнайте, как [устранить неполадки в шифровании azure Disk](disk-encryption-troubleshooting.md)
- Прочитайте [пример скриптов шифрования дисков Azure](disk-encryption-sample-scripts.md)
