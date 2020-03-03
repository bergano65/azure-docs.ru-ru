---
title: Использование скриптов развертывания для шаблонов | Документация Майкрософт
description: Узнайте, как использовать скрипты развертывания в шаблонах Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 21725e64bb359b2f11086baceb186605f010b796
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561465"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Руководство по Использование скриптов развертывания для создания самозаверяющего сертификата (предварительная версия)

Здесь описывается, как использовать скрипты развертывания в шаблонах Azure Resource Manager. Скрипты развертывания позволяют выполнять настраиваемые действия, которые не могут быть выполнены с помощью шаблонов Resource Manager. Например, создание самозаверяющего сертификата.  Работая с этим учебником, вы создадите шаблон для развертывания хранилища ключей Azure, а затем используете ресурс `Microsoft.Resources/deploymentScripts` в том же шаблоне для создания сертификата и добавите сертификат в хранилище ключей. Дополнительные сведения об использовании скриптов развертывания в шаблонах Azure Resource Manager см. в [этой статье](./deployment-script-template.md).

> [!NOTE]
> Сейчас скрипт развертывания находится на этапе предварительной версии. Чтобы использовать его, необходимо [зарегистрироваться для использования предварительной версии](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Два ресурса скрипта развертывания, учетная запись хранения и экземпляр контейнера, создаются в одной группе ресурсов для выполнения сценариев и устранения неполадок. Эти ресурсы обычно удаляются службой скриптов, когда выполнение скрипта достигает конечного состояния. Плата взимается за ресурсы, пока они не будут удалены. Дополнительные сведения в разделе об [очистке ресурсов скриптов развертывания](./deployment-script-template.md#clean-up-deployment-script-resources).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Изменение шаблона
> * Развертывание шаблона
> * Отладка невыполненного скрипта.
> * Очистка ресурсов

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* **[Visual Studio Code](https://code.visualstudio.com/) с расширением средств диспетчера ресурсов**. Дополнительные сведения см. в статье [Use Visual Studio Code to create Azure Resource Manager templates](./use-vs-code-to-create-template.md) (Создание шаблонов Azure Resource Manager с помощью Visual Studio Code).

* **Назначаемое пользователем управляемое удостоверение с ролью участника на уровне подписки**. Это удостоверение используется для выполнения скриптов развертывания. Сведения о его создании см. в [этом разделе](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). Идентификатор удостоверения необходим при развертывании шаблона. Требуемый формат удостоверения:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Чтобы получить идентификатор, выполните следующий скрипт PowerShell, указав в нем имя группы ресурсов и имя удостоверения.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Чтобы не создавать шаблон с нуля, откройте его в [Шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/). Шаблоны быстрого запуска Azure — это репозиторий для шаблонов Resource Manager.

В этом кратком руководстве используется шаблон [Create an Azure Key Vault and a secret](https://azure.microsoft.com/resources/templates/101-key-vault-create/) (Создание Azure Key Vault и секрета). Шаблон создает хранилище ключей, а затем добавляет секрет в это хранилище.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="edit-the-template"></a>Изменение шаблона

Внесите следующие изменения в шаблон:

### <a name="clean-up-the-template-optional"></a>Очистка шаблона (необязательно)

Исходный шаблон добавляет секрет в хранилище ключей.  Чтобы упростить учебник, удалите следующий ресурс:

* **Microsoft.KeyVault/vaults/secrets**.

Удалите следующие два определения параметров:

* **secretName**;
* **secretValue**.

Если вы решили не удалять эти определения, необходимо указать значения параметров во время развертывания.

### <a name="configure-the-key-vault-access-policies"></a>Настройка политик доступа к хранилищу ключей

Скрипт развертывания добавляет сертификат в хранилище ключей. Настройте политики доступа к хранилищу ключей, чтобы предоставить разрешение управляемому удостоверению:

1. Добавьте параметр для получения идентификатора управляемого удостоверения:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Расширение шаблона Resource Manager в Visual Studio Code еще не предоставляет возможность форматирования скриптов развертывания. Не нажимайте клавиши [SHIFT]+[ALT]+F для форматирования ресурсов deploymentScripts, как в приведенном ниже примере.

1. Добавьте параметр для настройки политик доступа к хранилищу ключей, чтобы управляемое удостоверение могло добавлять сертификаты в это хранилище.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Обновите существующие политики доступа к хранилищу ключей, указав:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Определены две политики: одна для пользователя, выполнившего вход, а другая для управляемого удостоверения.  Для проверки развертывания пользователю, выполнившему вход, требуется только разрешение на *создание списка*.  Для упрощения этого учебника управляемому удостоверению и пользователям, выполнившим вход, назначается один и тот же сертификат.

### <a name="add-the-deployment-script"></a>Добавление скрипта развертывания

1. Добавьте три параметра, используемые скриптом развертывания.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Добавьте ресурс deploymentScripts.

    > [!NOTE]
    > Так как встроенные скрипты развертывания заключены в двойные кавычки, строки внутри этих скриптов необходимо заключить в одинарные кавычки. Escape-символ для PowerShell — **&#92;** .

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    Ресурс `deploymentScripts` зависит от ресурса хранилища ключей и ресурса назначения роли.  Он имеет следующие свойства:

    * **identity**. Скрипт развертывания использует назначаемое пользователем управляемое удостоверение для выполнения скриптов.
    * **kind**. Укажите тип скрипта. В настоящее время поддерживается только скрипт PowerShell.
    * **forceUpdateTag**. Определите, следует ли выполнять скрипт развертывания, даже если источник скрипта не изменился. Может иметь значение текущей метки времени или GUID. Дополнительные сведения о выполнении скрипта несколько раз см. [здесь](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**. Укажите используемую версию модуля Azure PowerShell. В настоящее время скрипт развертывания поддерживает версии 2.7.0, 2.8.0 и 3.0.0.
    * **timeout**. Укажите максимально допустимое время выполнения скрипта в [формате ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Значение по умолчанию — **P1D**.
    * **arguments**. Укажите значения параметров. Значения разделяются пробелами.
    * **scriptContent**. Укажите содержимое скрипта. Чтобы запустить внешний скрипт, используйте вместо этого свойство **primaryScriptURI**. Дополнительные сведения об использовании внешнего скрипта см. [здесь](./deployment-script-template.md#use-external-scripts).
        Объявление **$DeploymentScriptOutputs** требуется только при тестировании скрипта на локальном компьютере. Объявление переменной позволяет выполнять скрипт на локальном компьютере и в ресурсе deploymentScript без внесения изменений. Значение, присваиваемое $DeploymentScriptOutputs, доступно в виде выходных данных в развертываниях. Дополнительную информацию см. в разделах о работе с выходными данными скриптов развертывания [PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) или [CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **cleanupPreference**. Укажите, в каком случае необходимо удалять ресурсы скрипта развертывания.  Значение по умолчанию — **Always**. Это означает, что ресурсы скрипта развертывания удаляются вне зависимости от конечного состояния (выполнено, сбой, отмена). В этом учебнике используется значение **OnSuccess**, чтобы вы могли просмотреть результаты выполнения скрипта.
    * **retentionInterval**. Укажите интервал, в течение которого служба будет хранить ресурсы скрипта после достижения конечного состояния. По истечении этого времени ресурсы будут удалены. Длительность основывается на шаблоне ISO 8601. В этом учебнике используется P1D, что означает один день.  Это свойство используется, если для параметра **cleanupPreference** установлено значение **OnExpiration**. Это свойство сейчас не включено.

    Скрипт развертывания принимает три параметра: имя хранилища ключей, имя сертификата и имя субъекта.  Он создает сертификат, а затем добавляет его в хранилище ключей.

    Параметр **$DeploymentScriptOutputs** используется для хранения выходных значений.  Дополнительные сведения см. в разделах о работе с выходными данными скриптов развертывания [PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) или [CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Готовый шаблон можно найти [здесь](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Чтобы увидеть процесс отладки, поместите ошибку в код, добавив следующую строку в скрипт развертывания:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Правильная команда — **Write-Output**, а не **Write-Output1**.

1. Чтобы сохранить файл выберите **Файл**>**Сохранить**.

## <a name="deploy-the-template"></a>Развертывание шаблона

Дополнительные сведения об открытии Cloud Shell и передаче файла шаблона в оболочку см. в разделе о [развертывании шаблона](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) в кратком руководстве по Visual Studio Code. После этого выполните следующий скрипт PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

Службе скрипта развертывания необходимо создать дополнительные ресурсы скрипта развертывания для его выполнения. В дополнение к фактическому времени выполнения сценария еще около одной минуты может занимать процесс подготовки и очистки.

Сбой развертывания произошел из-за недопустимой команды: в скрипте используется **Write-Output1**. Вы получите следующее сообщение об ошибке:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

Результат выполнения скрипта развертывания сохраняется в ресурсах этого скрипта для устранения неполадок.

## <a name="debug-the-failed-script"></a>Отладка невыполненного скрипта

1. Войдите на [портал Azure](https://portal.azure.com).
1. Откройте группу ресурсов. Это имя проекта с добавленным **rg**. В группе ресурсов должны отобразиться два дополнительных ресурса. Эти ресурсы называются *ресурсами скрипта развертывания*.

    ![Ресурсы скрипта развертывания шаблона Resource Manager](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Оба файла имеют суффикс **azscripts**. Один из них — учетная запись хранения, а второй — экземпляр контейнера.

    Выберите **Показывать скрытые типы**, чтобы получить список ресурсов deploymentScripts.

1. Выберите учетную запись хранения с суффиксом **azscripts**.
1. Выберите плитку **Общие папки**. Вы увидите папку **azscripts**.  Она содержит файлы выполнения скрипта развертывания.
1. Выберите **azscripts**. Вы должны увидеть две папки — **azscriptinput** и **azscriptoutput**.  Входная папка содержит системный файл скрипта PowerShell и пользовательские файлы скрипта развертывания. Выходная папка содержит файл **executionresult.json** и выходной файл скрипта. Сообщение об ошибке можно просмотреть в файле **executionresult.json**. Выходной файл отсутствует из-за ошибки выполнения.

Удалите строку **Write-Output1** и повторно разверните шаблон.

При успешном выполнении второго развертывания ресурсы скрипта развертывания должны быть удалены службой скриптов, так как для свойства **cleanupPreference** задано значение **OnSuccess**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Фильтровать по имени** введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы узнали, как использовать скрипт развертывания в шаблонах Azure Resource Manager. Сведения о том, как развернуть ресурсы Azure на основе условий, см. по ссылке ниже.

> [!div class="nextstepaction"]
> [Использование условий](./template-tutorial-use-conditions.md)
