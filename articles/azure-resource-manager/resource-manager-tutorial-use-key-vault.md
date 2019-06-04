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
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239244"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Руководство. Интеграция с Azure Key Vault при развертывании шаблона Resource Manager

Узнайте, как получить секреты из Azure Key Vault и передавать эти секреты в качестве параметров во время развертывания Resource Manager. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](./resource-manager-keyvault-parameter.md).

С помощью инструкций в [руководстве по настройке порядка развертывания ресурсов](./resource-manager-tutorial-create-templates-with-dependent-resources.md) вы создали виртуальную машину. Вам нужно предоставить имя и пароль администратора для виртуальной машины. Вместо предоставления пароля можно предварительно сохранить пароль в Azure Key Vault, а затем настроить шаблон для извлечения пароля из хранилища ключей во время развертывания.

![Схема интеграции Key Vault при развертывании шаблона Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Подготовка хранилища ключей.
> * открытие шаблона быстрого запуска;
> * Изменение файла параметров
> * Развертывание шаблона
> * Проверка развертывания
> * Очистка ресурсов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с [расширением Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Убедитесь, что созданный пароль соответствует требованиям к паролю виртуальной машины. У каждой службы Azure есть определенные требования к паролю. Чтобы увидеть требования к паролю виртуальной машины, обратитесь к разделу [Какие требования к паролю при создании виртуальной машины?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Подготовка хранилища ключей.

Из этого раздела вы узнаете, как создать хранилище ключей и добавить в него секрет, который будет получен при развертывании шаблона. Существует много способов создания хранилища ключей. В этом руководстве описывается, как развернуть [шаблон Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json) с помощью Azure PowerShell. Этот шаблон делает следующее.

* Создание хранилища ключей с помощью включенного свойства `enabledForTemplateDeployment`. Это свойство должно получить значение true до того, как процесс развертывания шаблона сможет получить доступ к секретам, определенным в этом хранилище ключей.
* Добавление секрета в хранилище ключей.  Секрет хранит пароль администратора виртуальной машины.

> [!NOTE]
> Если вы (в качестве пользователя для развертывания шаблона виртуальной машины) не являетесь владельцем или участником хранилища ключей, владелец или участник хранилища ключей должен предоставить вам доступ к разрешению Microsoft.KeyVault/vaults/deploy/action для хранилища ключей. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](./resource-manager-keyvault-parameter.md).

Для запуска следующего скрипта PowerShell выберите вкладку **Попробовать**, чтобы открыть Cloud Shell. Чтобы вставить скрипт, щелкните правой кнопкой на панели оболочки и выберите **Вставить**.

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

Некоторые важные моменты:

* Имя группы ресурсов — это имя проекта с добавлением **rg**. Чтобы было проще [удалить ресурсы, созданные при работе с этим руководством](#clean-up-resources), используйте то же имя проекта и имя группы ресурсов при [развертывании следующего шаблона](#deploy-the-template).
* Имя секрета по умолчанию — **vmAdminPassword**. Оно жестко запрограммировано в шаблоне.
* Чтобы шаблон мог получить секрет, необходимо включить политику доступа к хранилищу ключей, представленную параметром **Включить доступ к Azure Resource Manager для развертывания шаблонов**. В шаблоне эта политика включена. Подробные сведения об этой политике доступа см. в разделе о [развертывании хранилищ ключей и секретов](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

У шаблона есть выходное значение с именем **keyVaultId**. Запишите его. Вам понадобиться этот идентификатор при развертывании виртуальной машины. Формат идентификатора ресурса выглядит следующим образом:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

При копировании и вставке идентификатора он может быть разбит на несколько строк. Вам необходимо объединить строки и удалить лишние пробелы.

Чтобы проверить развертывание, выполните следующую команду PowerShell в той же панели оболочки, чтобы получить секрет в виде простого текста. Команду можно выполнить только в том же сеансе оболочки, так как она использует переменную $keyVaultName, определяемую в предыдущем скрипте PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Итак, вы подготовили хранилище ключей и секрет. В следующих разделах описано, как настроить существующий шаблон, чтобы получить секрет во время развертывания.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Чтобы открыть файл, выберите **Открыть**. Это тот же сценарий, который использовался в статье [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Шаблоном определено пять ресурсов:

   * `Microsoft.Storage/storageAccounts`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.
5. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.
6. Повторите шаги 1–4, чтобы открыть следующий URL-адрес, а затем сохраните файл как **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Изменение файла параметров

В файл шаблона не нужно вносить изменения.

1. Откройте файл **azuredeploy.parameters.json** в Visual Studio Code, если он еще не открыт.
2. Обновите параметр **adminPassword** до:

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
    > Замените значение **идентификатора** идентификатором ресурса своего хранилища ключей, созданного на предыдущем этапе.

    ![Интеграция Key Vault и файла параметров развертывания виртуальной машины шаблона Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Укажите значения для таких свойств:

    * **adminUsername**. Имя учетной записи администратора виртуальной машины.
    * **dnsLabelPrefix**: назовите dnsLabelPrefix.

    См. пример на предыдущем снимке экрана.

4. Сохраните изменения.

## <a name="deploy-the-template"></a>Развертывание шаблона

За инструкциями по развертыванию шаблона обратитесь к [этому разделу](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Вам нужно отправить оба файла — **azuredeploy.json** и **azuredeploy.parameters.json** в Cloud Shell. После этого выполните следующий скрипт PowerShell, чтобы развернуть шаблон:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

При развертывании шаблона используйте ту же группу ресурсов, что и для хранилища ключей. Это упрощает очистку ресурсов. Вам нужно удалить одну группу ресурсов вместо двух.

## <a name="validate-the-deployment"></a>Проверка развертывания

После успешного развертывания виртуальной машины протестируйте вход в систему с помощью пароля, сохраненного в хранилище ключей.

1. Откройте [портал Azure](https://portal.azure.com).
2. Выберите **Группы ресурсов**/**Имя_вашей_группы_ресурсов >** /**simpleWinVM**
3. В верхней части страницы выберите **Подключение**.
4. Выберите **Скачать RDP-файл** и следуйте инструкциям, чтобы войти в виртуальную машину с помощью пароля, сохраненного в хранилище ключей.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого руководства вы получили секрет из Azure Key Vault и применили его в развертывании шаблона.  Чтобы узнать, как создавать связанные шаблоны, обратитесь к статье:

> [!div class="nextstepaction"]
> [Tutorial: Create linked Azure Resource Manager templates](./resource-manager-tutorial-create-linked-templates.md) (Руководство по созданию связанных шаблонов Azure Resource Manager)
