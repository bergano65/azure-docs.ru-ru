---
title: Секрет Key Vault в шаблоне Azure Resource Manager | Документация Майкрософт
description: Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: a885fda23bb76091705ebe388f40a6eae7b56416
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351515"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания

Если необходимо передать защищенное значение (например, пароль) в качестве параметра во время развертывания, можно извлечь его из [Azure Key Vault](../key-vault/key-vault-whatis.md). Для получения значения нужно указать в параметре ссылку на хранилище ключей и секрет. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Хранилище ключей не обязательно должно находится в той же подписке, что и группа ресурсов, развертывание в которую выполняется.

## <a name="deploy-a-key-vault-and-secret"></a>Развертывание хранилища ключей и секретного кода

Чтобы создать хранилище ключей и секрет, используйте Azure CLI или PowerShell. `enabledForTemplateDeployment` — это свойство хранилища ключей. Чтобы получить доступ к секретам внутри этого хранилища ключей из развертывания Resource Manager, свойству `enabledForTemplateDeployment` должно быть присвоено значение `true`. 

В приведенном ниже примере сценария Azure PowerShell и Azure CLI показано, как создать хранилище ключей и секрет.

Для интерфейса командной строки Azure:

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

Для PowerShell используйте команду:

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

В случае запуска сценария PowerShell за пределами Cloud Shell, чтобы создать пароль, используйте следующую команду:

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

Сведения об использовании шаблона Resource Manager. Пошаговые инструкции см. в [руководстве Интеграция с Azure Key Vault при развертывании шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> У каждой службы Azure есть определенные требования к паролю. Например, требования для виртуальных машин Azure см. в разделе.[Какие требования к паролю при создании виртуальной машины?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

## <a name="enable-access-to-the-secret"></a>Разрешение доступа к секрету

Кроме свойства `enabledForTemplateDeployment` с установленным значением `true` пользователь, развертывающий шаблон, должен иметь разрешение `Microsoft.KeyVault/vaults/deploy/action` для области, которая содержит Key Vault, в том числе группу ресурсов и хранилище ключей. Оно имеется у ролей [Владелец](../role-based-access-control/built-in-roles.md#owner) и [Участник](../role-based-access-control/built-in-roles.md#contributor). Если вы создаете хранилище ключей, у вас, как у владельца, есть разрешение. Если Key Vault находится в другой подписке, доступ предоставляется владельцем этого Key Vault.

Ниже показано, как создать роль с минимальным разрешением и назначить пользователя
1. Создание JSON-файла определения пользовательской роли

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Замените "00000000-0000-0000-0000-000000000000" на идентификатор подписки пользователя, которому необходимо развернуть шаблоны.

2. Создание новой роли с помощью JSON-файла:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    Пример `New-AzureRmRoleAssignment` назначает для пользователя пользовательскую роль на уровне группы ресурсов.  

При использовании хранилища Key Vault с шаблоном для [управляемого приложения](../managed-applications/overview.md) необходимо предоставить доступ к субъекту-службе **Поставщик ресурсов устройств**. Дополнительные сведения см. в статье [Доступ к секрету Key Vault при развертывании Управляемых приложений Azure](../managed-applications/key-vault-access.md).

## <a name="reference-a-secret-with-static-id"></a>Ссылка на секрет со статическим идентификатором

Шаблон, получающий секрет хранилища ключей, аналогичен любому другому шаблону. Это происходит, потому что **вы ссылаетесь на хранилище ключей в файле параметров, а не в шаблоне**. На следующем рисунке показано, как файл параметров ссылается на секрет и передает это значение в шаблон.

![Статический идентификатор](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Например, в [приведенном ниже шаблоне](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) развертывается база данных SQL, которая содержит пароль администратора. В качестве параметра пароля задается защищенная строка. Но в шаблоне не указывается источник этого значения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Теперь создайте файл параметров для предыдущего шаблона. В файле параметров укажите параметр, который совпадает с именем параметра в шаблоне. Для значения параметра используйте ссылку на секрет из хранилища ключей. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода. В [приведенном ниже файле параметров](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json) секрет хранилища ключей уже должен существовать. При этом для идентификатора ресурса используется статическое значение. Скопируйте этот файл локально и задайте идентификатор подписки, имя хранилища и имя сервера SQL Server.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Если необходимо использовать версию секрета, отличную от текущей, примените свойство `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Теперь разверните шаблон и передайте файл параметров. Можно воспользоваться примером шаблона из GitHub, однако необходимо использовать локальный файл параметров со значениями, заданными для вашей среды.

Для интерфейса командной строки Azure:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Для PowerShell используйте команду:

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Ссылка на секрет с динамическим идентификатором

Выше мы рассмотрели способ передачи статического идентификатора ресурса для секрета хранилища ключей из параметра. Однако в некоторых сценариях вам нужно использовать ссылку на секретный код хранилища ключей, который изменяется в зависимости от текущего развертывания. Или можно просто передать в шаблон значения параметров, а не создавать ссылочный параметр в файле параметров. В любом случае можно динамически создать идентификатор ресурса для секрета хранилища ключей с помощью связанного шаблона.

Динамически создавать идентификатор ресурса в файле параметров нельзя, так как в файле параметров не разрешены выражения шаблонов. 

Добавьте связанный шаблон в родительский шаблон и передайте параметр, содержащий динамически созданный идентификатор ресурса. На следующем рисунке показано, как параметр в связанном шаблоне ссылается на секрет.

![Динамический идентификатор](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[Следующий шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) динамически создает идентификатор хранилища ключей и передает его в качестве параметра.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Разверните предыдущий шаблон и укажите значения для параметров. Можно воспользоваться примером шаблона из GitHub, однако необходимо указать значения параметров для вашей среды.

Для интерфейса командной строки Azure:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
