---
title: Секрет Ключа Убежище с шаблоном
description: Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 08b4042c6bad83f13ebaea0f46046ea7707fd868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460200"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания

Вместо того, чтобы поместить безопасное значение (например, пароль) непосредственно в шаблон или файл параметров, можно получить значение из [Хранилища ключей Azure](../../key-vault/key-vault-overview.md) во время развертывания. Для получения значения нужно указать в параметре ссылку на хранилище ключей и секрет. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Хранилище ключей может существовать в другой подписке, чем группа ресурсов, к ней развертывается.

В этой статье основное внимание уделяется сценарию прохождения чувствительного значения в качестве параметра шаблона. Он не охватывает сценарий установки виртуального свойства машины на URL-адрес сертификата в Хранилище ключей. Для шаблона быстрого запуска этого сценария смотрите [сертификат из Убежища ключей Azure на виртуальной машине.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

## <a name="deploy-key-vaults-and-secrets"></a>Развертывание хранилищ Key Vault и секретов

Чтобы получить доступ к своду `enabledForTemplateDeployment` ключей во `true`время развертывания шаблона, установите на хранилище ключей для

Если у вас уже есть Key Vault, убедитесь, что он позволяет развертывание шаблонов.

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Чтобы создать новый Key Vault и добавить секрет, используйте:

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Как владелец хранилища ключей, вы автоматически имеете доступ к созданию секретов. Если пользователь, работающий с секретами, не является владельцем хранилища ключей, предоставите доступ с:

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Для получения дополнительной информации о создании хранилищ ключей и добавлении секретов см.

- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью Azure CLI](../../key-vault/quick-create-cli.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью PowerShell](../../key-vault/quick-create-powershell.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью портала Azure](../../key-vault/quick-create-portal.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET](../../key-vault/quick-create-net.md)
- [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения Node](../../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Предоставление доступа к секретам

Пользователь, развертыващий шаблон, должен `Microsoft.KeyVault/vaults/deploy/action` иметь разрешение на область действия группы ресурсов и хранилище ключей. Оно имеется у ролей [Владелец](../../role-based-access-control/built-in-roles.md#owner) и [Участник](../../role-based-access-control/built-in-roles.md#contributor). Если вы создали хранилище ключей, вы являетесь владельцем, так что у вас есть разрешение.

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
    Замените "00000000-0000-0000-0000-0000000000000000000000000000000" с идентификатором подписки.

2. Создание новой роли с помощью JSON-файла:

    # <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Образцы присваивают пользовательской роли пользователю на уровне группы ресурсов.

При использовании хранилища Key Vault с шаблоном для [управляемого приложения](../managed-applications/overview.md) необходимо предоставить доступ к субъекту-службе **Поставщик ресурсов устройств**. Дополнительные сведения см. в статье [Доступ к секрету Key Vault при развертывании Управляемых приложений Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Секреты ссылки со статическим идентификатором

Этот способ дает возможность ссылаться на хранилище ключей в файле параметров, а не в шаблоне. На следующем рисунке показано, как файл параметров ссылается на секрет и передает это значение в шаблон.

![Диаграмма статического идентификатора интеграции хранилища ключей Resource Manager](./media/key-vault-parameter/statickeyvault.png)

[Учебник: Интеграция Azure Key Vault в развертывание шаблона управления ресурсами](./template-tutorial-use-key-vault.md) использует этот метод.

Следующий шаблон развертывает сервер S'L, который включает пароль администратора. В качестве параметра пароля задается защищенная строка. Но в шаблоне не указывается, откуда это значение.

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
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
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

В приведенном ниже файле параметров секрет хранилища ключей уже должен существовать. При этом для идентификатора ресурса используется статическое значение.

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

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Ссылка на секреты с динамическим идентификатором

Выше мы рассмотрели способ передачи статического идентификатора ресурса для секрета хранилища ключей из параметра. Однако в некоторых сценариях вам нужно использовать ссылку на секретный код хранилища ключей, который изменяется в зависимости от текущего развертывания. Или можете просто передать значения параметра в шаблон вместо создания ссылочного параметра в файле параметров. В любом случае можно динамически создать идентификатор ресурса для секрета хранилища ключей с помощью связанного шаблона.

Динамически создавать идентификатор ресурса в файле параметров нельзя, так как в файле параметров не разрешены выражения шаблонов.

В родительский шаблон вы добавляете вложенный шаблон и проходите в параметр, содержащий динамически генерируемый идентификатор ресурса. На следующем рисунке показано, как параметр в связанном шаблоне ссылается на секрет.

![Динамический идентификатор](./media/key-vault-parameter/dynamickeyvault.png)

Следующий шаблон динамически создает идентификатор хранилища ключей и передает его в качестве параметра.

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
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
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
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о хранилищах ключей см. в статье [Что такое хранилище ключей Azure?](../../key-vault/key-vault-overview.md)
- Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
