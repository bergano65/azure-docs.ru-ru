---
title: Секрет Key Vault в шаблоне Azure Resource Manager | Документация Майкрософт
description: Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: 489b09d2523393ae67668ed13c651c9b7b0217b4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998893"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания

Вместо того, чтобы размещать безопасное значение (например, пароль) непосредственно в шаблоне или файле параметров, можно извлечь значение из [Azure Key Vault](../key-vault/key-vault-overview.md) во время развертывания. Для получения значения нужно указать в параметре ссылку на хранилище ключей и секрет. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Хранилище ключей может существовать в той же подписке, что и группа ресурсов, в которую выполняется развертывание.

## <a name="deploy-key-vaults-and-secrets"></a>Развертывание хранилищ Key Vault и секретов

Чтобы получить доступ к хранилищу ключей во время развертывания `enabledForTemplateDeployment` шаблона, задайте для `true`хранилища ключей значение.

В следующих примерах Azure CLI и Azure PowerShell показано, как создать хранилище ключей и добавить секрет.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Как владелец хранилища ключей, вы автоматически имеете доступ к созданию секретов. Если пользователь, работающий с секретами, не является владельцем хранилища ключей, предоставьте доступ с помощью:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Дополнительные сведения о создании хранилищ ключей и добавлении секретов см. в следующих статьях:

- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью Azure CLI](../key-vault/quick-create-cli.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью PowerShell](../key-vault/quick-create-powershell.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью портала Azure](../key-vault/quick-create-portal.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET](../key-vault/quick-create-net.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения Node](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Предоставление доступа к секретам

Пользователь, который развертывает шаблон, должен иметь `Microsoft.KeyVault/vaults/deploy/action` разрешение на доступ к области группы ресурсов и хранилища ключей. Оно имеется у ролей [Владелец](../role-based-access-control/built-in-roles.md#owner) и [Участник](../role-based-access-control/built-in-roles.md#contributor). Если вы создали хранилище ключей, вы являетесь владельцем и имеете разрешение.

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
    Замените "00000000-0000-0000-0000-000000000000" на идентификатор подписки.

2. Создание новой роли с помощью JSON-файла:

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    В примерах пользовательская роль назначается пользователю на уровне группы ресурсов.  

При использовании хранилища Key Vault с шаблоном для [управляемого приложения](../managed-applications/overview.md) необходимо предоставить доступ к субъекту-службе **Поставщик ресурсов устройств**. Дополнительные сведения см. в статье [Доступ к секрету Key Vault при развертывании Управляемых приложений Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Секреты ссылки со статическим идентификатором

Этот способ дает возможность ссылаться на хранилище ключей в файле параметров, а не в шаблоне. На следующем рисунке показано, как файл параметров ссылается на секрет и передает это значение в шаблон.

![Диаграмма статического идентификатора интеграции хранилища ключей Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Учебник. Интеграция с Azure Key Vault при развертывании шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md) использует этот метод.

Следующий шаблон развертывает сервер SQL Server, который содержит пароль администратора. В качестве параметра пароля задается защищенная строка. Но шаблон не указывает, откуда берется это значение.

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

Теперь создайте файл параметров для предыдущего шаблона. В файле параметров укажите параметр, который совпадает с именем параметра в шаблоне. Для значения параметра используйте ссылку на секрет из хранилища ключей. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секрета.

В следующем файле параметров секрет хранилища ключей уже должен существовать, и вы предоставляете статическое значение для его идентификатора ресурса.

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
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
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
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Разверните шаблон и передайте файл параметров.

Для интерфейса командной строки Azure:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Для PowerShell используйте команду:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Ссылка на секреты с динамическим идентификатором

Выше мы рассмотрели способ передачи статического идентификатора ресурса для секрета хранилища ключей из параметра. Однако в некоторых сценариях вам нужно использовать ссылку на секретный код хранилища ключей, который изменяется в зависимости от текущего развертывания. Или можете просто передать значения параметра в шаблон вместо создания ссылочного параметра в файле параметров. В любом случае можно динамически создать идентификатор ресурса для секрета хранилища ключей с помощью связанного шаблона.

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

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Для PowerShell используйте команду:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о хранилищах ключей см. в статье [Что такое хранилище ключей Azure?](../key-vault/key-vault-overview.md)
- Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
