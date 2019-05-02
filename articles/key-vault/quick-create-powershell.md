---
title: Краткое руководство Azure. Настройка и получение секрета из Key Vault с помощью PowerShell | Документация Майкрософт
description: ''
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: eb9a18ad0fb00fc9f648e3b22b0db1ea4eda479d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712754"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault — это облачная служба, которая работает как защищенное хранилище секретов. Вы можете безопасно хранить ключи, пароли, сертификаты и другие секреты. Дополнительные сведения о хранилище ключей см. в статье [обзора](key-vault-overview.md). В этом кратком руководстве для создания хранилища ключей вы используете PowerShell. Затем вы сохраните секрет в только что созданном хранилище.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, понадобится модуль Azure PowerShell 1.0.0 или более поздней версии. Чтобы узнать версию, введите `$PSVersionTable.PSVersion`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzAccount`, чтобы создать подключение к Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>создать хранилище ключей;

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
* **URI хранилища**. В нашем примере это https://contosokeyvault.vault.azure.net/. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

После создания хранилища ваша учетная запись Azure будет единственной учетной записью, с которой можно выполнять любые действия в новом хранилище.

![Выходные данные после выполнения команды, создающей хранилище ключей](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Добавление секрета в Key Vault

Чтобы добавить секрет в хранилище, вам просто нужно выполнить несколько шагов. В этом случае вы добавите пароль, который может быть использован приложением. Пароль называется **ExamplePassword**, и в нем хранится значение **hVFkk965BuUv**.

Сначала преобразуйте значение **hVFkk965BuUv** в безопасную строку, выполнив следующую команду:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Затем введите команды PowerShell ниже, чтобы создать секрет в Key Vault с именем **ExamplePassword** и значением **hVFkk965BuUv**:

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Вы создали Key Vault, сохранили в нем секрет и извлекли его.

## <a name="clean-up-resources"></a>Очистка ресурсов

 Другие руководства в этой серии созданы на основе этого документа. Если вы планируете продолжить работу с другими краткими руководствами и статьями, эти ресурсы можно не удалять.

Вы можете удалить ставшие ненужными группу ресурсов, хранилище ключей и все связанные с ним ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали Key Vault и сохранили в нем ключ ПО. Чтобы получить дополнительные сведения о Key Vault и о том, как вы можете использовать его в своих приложениях, ознакомьтесь с руководством по веб-приложениям, работающим с Key Vault.

Чтобы узнать, как считать секрет из хранилища ключей с помощью веб-приложения, используя управляемые удостоверения для ресурсов Azure, см. руководство по

> [!div class="nextstepaction"]
> [настройке веб-приложения Azure для чтения секретов из хранилища ключей](quick-create-net.md).
