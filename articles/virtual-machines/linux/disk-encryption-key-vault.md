---
title: Создание и Настройка хранилища ключей для шифрования дисков Azure
description: В этой статье описаны действия по созданию и настройке хранилища ключей для использования с шифрованием дисков Azure.
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 90306c55d976670f432d146d94764c4d90b8667d
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828501"
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

- [Создание и шифрование виртуальной машины Linux с помощью Azure CLI](disk-encryption-cli-quickstart.md)
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

## <a name="create-a-resource-group"></a>Создание группы ресурсов

*Если у вас уже есть группа ресурсов, можно перейти к [разделу Создание хранилища ключей](#create-a-key-vault).*

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

Создайте группу ресурсов с помощью команды [AZ Group create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI, команды [New-азресаурцеграуп](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell или из [портал Azure](https://portal.azure.com).

### <a name="azure-cli"></a>Интерфейс командной строки Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

*Если у вас уже есть хранилище ключей, можно пропустить [установку политик расширенного доступа к хранилищу ключей](#set-key-vault-advanced-access-policies).*

Создайте хранилище ключей с помощью команды [AZ keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI, команды [New-азкэйваулт](/powershell/module/az.keyvault/new-azkeyvault) Azure PowerShell, [портал Azure](https://portal.azure.com)или [шаблона диспетчер ресурсов](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Чтобы обеспечить, что секреты шифрования не пересекают региональные границы, шифрование дисков Azure требует, чтобы Key Vault и виртуальные машины размещались в одном регионе. Создайте и используйте Key Vault в том же регионе, что и виртуальные машины для шифрования. 

Каждый Key Vault должен иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

### <a name="azure-cli"></a>Интерфейс командной строки Azure

При создании хранилища ключей с помощью Azure CLI добавьте флаг "--Enabled-to-Disk-Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

При создании хранилища ключей с помощью Azure PowerShell добавьте флаг "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Шаблон Resource Manager

Хранилище ключей также можно создать с помощью [шаблона диспетчер ресурсов](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. В шаблоне быстрого запуска Azure щелкните **Deploy to Azure** (Развернуть в Azure).
2. Выберите подписку, группу ресурсов, расположение группы ресурсов, Key Vault имя, идентификатор объекта, юридические условия и соглашение, а затем щелкните **приобрести**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Настройка политик расширенного доступа к хранилищу ключей

Платформа Azure должна иметь доступ к ключам шифрования или секретам, расположенным в вашем хранилище ключей, чтобы предоставлять их виртуальной машине при ее загрузке для расшифровки томов. 

Если вы не включили хранилище ключей для шифрования дисков, развертывания или развертывания шаблона во время создания (как показано на предыдущем шаге), необходимо обновить его политики расширенного доступа.  

### <a name="azure-cli"></a>Интерфейс командной строки Azure

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
 Используйте командлет PowerShell для хранилища ключей [Set-азкэйваултакцессполици](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , чтобы включить шифрование дисков для хранилища ключей.

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

### <a name="azure-portal"></a>портале Azure

1. Выберите хранилище ключей, перейдите к разделу **политики доступа**и **щелкните, чтобы отобразить дополнительные политики доступа**.
2. Установите флажок **Включить доступ к шифрованию дисков Azure для шифрования томов**.
3. Выберите **Включить доступ к виртуальным машинам Azure для развертывания** или **Включить доступ к Azure Resource Manager для развертывания шаблонов**, если это необходимо. 
4. Нажмите кнопку **Сохранить**.

    ![Установка политики расширенного доступа к хранилищу Azure Key Vault](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Настройка ключа шифрования ключа (KEK)

Если вы хотите использовать ключ шифрования ключей (KEK), чтобы добавить уровень безопасности ключей шифрования, поместите KEK в хранилище ключей. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault.

Вы можете создать новый KEK с помощью команды Azure CLI [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , командлета Azure PowerShell [Add-азкэйваулткэй](/powershell/module/az.keyvault/add-azkeyvaultkey) или [портал Azure](https://portal.azure.com/). Необходимо создать тип ключа RSA. Шифрование дисков Azure пока не поддерживает использование ключей эллиптической кривой.

Вместо этого можно импортировать KEK из локального АППАРАТного модуля управления ключами. Дополнительные сведения см. в документации по [Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). 

URL-адреса KEK хранилища ключей должны иметь версию. Это требование Azure. Ниже приведены примеры действительных URL-адресов секрета и ключа шифрования ключей.

* Пример допустимого URL-адреса секрета: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Пример допустимого URL-адреса KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Шифрование дисков Azure не поддерживает указание номеров портов в URL-адресах секрета и ключа шифрования ключей для хранилища ключей. Ниже приведены примеры недопустимых и допустимых URL-адресов хранилища ключей.

  * Допустимый URL-адрес хранилища ключей: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Недопустимый URL-адрес хранилища ключей: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Интерфейс командной строки Azure

Используйте команду Azure CLI [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , чтобы создать новый KEK и сохранить его в хранилище ключей.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Вместо этого можно импортировать закрытый ключ с помощью команды Azure CLI [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

В любом случае укажите имя Azure CLI KEK в параметре [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --ключ-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Используйте командлет Azure PowerShell [Add-азкэйваулткэй](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) , чтобы создать новый KEK и сохранить его в хранилище ключей.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Вместо этого можно импортировать закрытый ключ с помощью команды Azure PowerShell [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

В любом случае вы укажите идентификатор хранилища ключей KEK и URL-адрес KEK в параметрах Azure PowerShell [Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -кэйенкриптионкэйваултид и-KeyEncryptionKeyUrl. Обратите внимание, что в этом примере предполагается, что вы используете одно и то же хранилище ключей как для ключа шифрования диска, так и для KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Дополнительная информация

- [Сценарий CLI для предварительных требований к шифрованию дисков Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Сценарий PowerShell для предварительных требований к шифрованию дисков Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Сведения о [сценариях шифрования дисков Azure на виртуальных машинах Linux](disk-encryption-linux.md)
- Сведения об [устранении неполадок шифрования дисков Azure](disk-encryption-troubleshooting.md)
- Ознакомьтесь с [примерами сценариев шифрования дисков Azure](disk-encryption-sample-scripts.md) .
