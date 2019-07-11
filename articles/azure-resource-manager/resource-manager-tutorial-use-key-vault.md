---
title: Интеграция Azure Key Vault в развертывание шаблона Azure Resource Manager | Документация Майкрософт
description: Узнайте, как использовать Azure Key Vault для передачи значений безопасного параметра во время развертывания шаблона Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436574"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Руководство по интеграции Azure Key Vault в развертывание шаблона Resource Manager

Узнайте, как получить секреты из Azure Key Vault и передавать эти секреты в качестве параметров во время развертывания Azure Resource Manager. Значение параметра никогда не предоставляется, так как указывается только его идентификатор в хранилище ключей. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](./resource-manager-keyvault-parameter.md).

С помощью инструкций из [учебника по настройке порядка развертывания ресурсов](./resource-manager-tutorial-create-templates-with-dependent-resources.md) вы создадите виртуальную машину. Вам нужно предоставить имя и пароль учетной записи администратора виртуальной машины. Чтобы не предоставлять пароль, можно предварительно сохранить его в Azure Key Vault, а затем настроить шаблона для извлечения пароля из хранилища ключей во время развертывания.

![Схема, отображающая процесс интеграции шаблона Resource Manager с хранилищем ключей](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Подготовка хранилища ключей.
> * Открытие шаблона быстрого запуска
> * Изменение файла параметров
> * Развертывание шаблона
> * Проверка развертывания
> * Очистка ресурсов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с [расширением средств Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* С целью повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Убедитесь, что созданный пароль соответствует требованиям к паролю виртуальной машины. У каждой службы Azure есть определенные требования к паролю. Требования к паролю виртуальной машины см. в разделе [Какие требования к паролю при создании виртуальной машины?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Подготовка хранилища ключей.

Из этого раздела вы узнаете, как создать хранилище ключей и добавить в него секрет, который будет получен при развертывании шаблона. Существует много способов создания хранилища ключей. В этом руководстве описывается, как развернуть [шаблон Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json) с помощью Azure PowerShell. Этот шаблон делает следующее:

* Создает хранилище ключей с включенным свойством `enabledForTemplateDeployment`. Это свойство должно получить значение *true* до того, как процесс развертывания шаблона сможет получить доступ к секретам, определенным в этом хранилище ключей.
* Добавляет секрет в хранилище ключей. Секрет хранит пароль учетной записи администратора виртуальной машины.

> [!NOTE]
> Если вы (в качестве пользователя для развертывания шаблона виртуальной машины) не являетесь владельцем или участником хранилища ключей, владелец или участник хранилища ключей должен предоставить вам доступ к разрешению *Microsoft.KeyVault/vaults/deploy/action* для хранилища ключей. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](./resource-manager-keyvault-parameter.md).

Чтобы запустить следующий сценарий Azure PowerShell, выберите вкладку **Попробовать** для открытия Azure Cloud Shell. Чтобы вставить скрипт, щелкните правой кнопкой на панели оболочки и выберите **Вставить**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

> [!IMPORTANT]
> * Обратите внимание, что имя группы ресурсов — это имя проекта с добавлением **rg**. Чтобы было проще [удалить ресурсы, созданные при работе с этим учебником](#clean-up-resources), используйте то же имя проекта и имя группы ресурсов при [развертывании следующего шаблона](#deploy-the-template).
> * Имя секрета по умолчанию — **vmAdminPassword**. Оно встроено в шаблон.
> * Чтобы шаблон мог извлечь секрет, необходимо активировать политику доступа к хранилищу ключей, предоставленную параметром "Включить доступ к Azure Resource Manager для развертывания шаблонов". В шаблоне эта политика включена. Подробные сведения об этой политике доступа см. в разделе о [развертывании хранилищ ключей и секретов](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

У шаблона есть выходное значение с именем *keyVaultId*. Запишите значение идентификатора для дальнейшего использования. Он понадобится при развертывании виртуальной машины. Формат идентификатора ресурса выглядит следующим образом:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

При копировании и вставке идентификатора он может быть разбит на несколько строк. Вам необходимо объединить строки и удалить лишние пробелы.

Чтобы проверить развертывание, выполните следующую команду PowerShell в той же панели оболочки, чтобы получить секрет в виде простого текста. Сценарий работает только в том же сеансе оболочки, так как используется переменная *$keyVaultName*, которая определена в приведенном выше сценарии PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Хранилище ключей и секрет подготовлены. В следующих разделах описано, как настроить существующий шаблон, чтобы получить секрет во время развертывания.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure — это репозиторий для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом учебнике, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл** > **Открыть файл**.

1. В поле **Имя файла** вставьте следующий URL-адрес:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Чтобы открыть файл, выберите **Открыть**. Сценарий аналогичен тому, который используется в статье [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   Шаблон определяет пять ресурсов:

   * `Microsoft.Storage/storageAccounts`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Рекомендуется получить основные сведения о шаблоне, прежде чем переходить к его настройке.

1. Выберите **Файл** > **Сохранить как**, а затем сохраните на свой локальный компьютер копию файла под именем *azuredeploy.json*.

1. Повторите шаги 1–3, чтобы открыть следующий URL-адрес, а затем сохраните файл как *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Изменение файла параметров

В файл шаблона не нужно вносить изменения.

1. В Visual Studio Code откройте файл *azuredeploy.parameters.json*, если он еще не открыт.
1. Укажите для параметра `adminPassword` следующий код.

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Замените значение для **id** на идентификатор ресурса хранилища ключей, созданного во время предыдущей процедуры.

    ![Интеграция Key Vault и файла параметров развертывания виртуальной машины шаблона Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Обновите следующие значения:

    * **adminUsername**. Имя учетной записи администратора виртуальной машины.
    * **dnsLabelPrefix**. Дайте имя значению dnsLabelPrefix.

    Примеры имен см. на предыдущем рисунке.

1. Сохраните изменения.

## <a name="deploy-the-template"></a>Развертывание шаблона

Следуйте указаниям в этом разделе о [развертывании шаблона](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Отправьте оба файла (*azuredeploy.json* и *azuredeploy.parameters.json*) в Cloud Shell. После этого выполните следующий сценарий PowerShell для развертывания шаблона.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

При развертывании шаблона используйте ту же группу ресурсов, что и для хранилища ключей. Такой подход упрощает процесс чистки ресурсов, так как вам необходимо удалить одну группу вместо двух.

## <a name="validate-the-deployment"></a>Проверка развертывания

После успешного развертывания виртуальной машины проверьте учетные данные входа в систему с помощью пароля, сохраненного в хранилище ключей.

1. Откройте [портал Azure](https://portal.azure.com).

1. Выберите **Группа ресурсов** >  **\<*имя вашей группы ресурсов*>**  > **simpleWinVM**.
1. Выберите **Подключение** в верхней части страницы.
1. Выберите **Скачать RDP-файл** и следуйте инструкциям, чтобы войти в виртуальную машину с помощью пароля, сохраненного в хранилище ключей.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, очистите их, удалив группу ресурсов.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого учебника вы извлекли секрет из хранилища ключей Azure, а затем использовали этот секрет при развертывании шаблона. Чтобы узнать, как создавать связанные шаблоны, обратитесь к статье:

> [!div class="nextstepaction"]
> [Tutorial: Create linked Azure Resource Manager templates](./resource-manager-tutorial-create-linked-templates.md) (Руководство по созданию связанных шаблонов Azure Resource Manager)
