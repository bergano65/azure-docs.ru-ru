---
title: Отправка электронной почты из модуля Runbook службы автоматизации Azure
description: В этой статье описывается, как отправить электронную почту из runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a92f65bd88a5aec79a179a6e2d53de15c274add4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834566"
---
# <a name="send-an-email-from-a-runbook"></a>Отправка сообщения электронной почты из runbook

Сообщение электронной почты из модуля Runbook можно отправить с помощью [SendGrid](https://sendgrid.com/solutions), используя PowerShell. 

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Учетная запись службы автоматизации](automation-offering-get-started.md) с модулями **Az**.
* [Учетная запись запуска от имени](automation-create-runas-account.md) для хранения и выполнения runbook.

## <a name="create-an-azure-key-vault"></a>создать Azure Key Vault;

Экземпляр Azure Key Vault можно создать, используя описанный ниже скрипт PowerShell. Замените значения переменной, выделенные курсивом, значениями для конкретной среды. Используйте внедренный компонент Azure Cloud Shell с помощью кнопки **Попробуйте!** , расположенной в правом верхнем углу блока кода. Также код можно скопировать и запустить локально на компьютере, где установлены [модули Az](/powershell/azure/install-az-ps).

> [!NOTE]
> Чтобы получить ключ API, выполните действия из статьи [Как найти ключ API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Другие способы создания хранилища ключей в Azure Key Vault и сохранения секрета см. в [кратких руководствах по Key Vault](/azure/key-vault/).

## <a name="import-required-modules-into-your-automation-account"></a>Импорт необходимых модулей в учетную запись службы автоматизации

Чтобы использовать Azure Key Vault в runbook, импортируйте следующие модули в учетную запись службы автоматизации:

    * [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile);
    * [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Инструкции по этому процессу есть в статье [Импорт модулей Az](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Создание модуля Runbook для отправки электронной почты

После того, как вы создали хранилище ключей и сохранили свой ключ API `SendGrid`, необходимо создать runbook для извлечения этого ключа API и отправки электронного письма. Наш runbook будет использовать `AzureRunAsConnection` в качестве [учетной записи запуска от имени](automation-create-runas-account.md) для аутентификации в Azure, чтобы получить секрет из Azure Key Vault. Мы присвоим этому runbook имя **Send-GridMailMessage**. Вы можете изменить скрипт PowerShell из этого примера и использовать его для других сценариев.

1. Перейдите к своей учетной записи службы автоматизации Azure.
2. В разделе **Автоматизация процессов** щелкните **Модули Runbook**.
3. В верхней части списка модулей runbook выберите **+ Создать Runbook**.
4. На странице "Добавление модуля Runbook" введите **Send-GridMailMessage** в качестве имени модуля Runbook. Для типа runbook выберите значение **PowerShell**. Затем выберите **Создать**.
   ![Создание модуля Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. После этого создается модуль runbook и откроется страница "Изменение Runbook PowerShell".
   ![Вносит изменения в модуль Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Скопируйте следующий пример PowerShell на страницу "Изменение". Убедитесь, что `VaultName` содержит имя, указанное при создании Key Vault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Нажмите кнопку **Опубликовать**, чтобы сохранить и опубликовать runbook.

Чтобы убедиться, что runbook выполняется успешно, выполните действия из статьи [Тестирование модуля Runbook](manage-runbooks.md#test-a-runbook) или [Start a runbook in Azure Automation](start-runbooks.md) (Запуск модуля Runbook в службе автоматизации Azure).

Если тестовое сообщение электронной почты не удастся получить сразу, проверьте папки **Нежелательная почта** и (или) **Спам**.

## <a name="clean-up-resources-after-the-email-operation"></a>Очистка ресурсов после операции электронной почты

1. Для этого выберите ненужный runbook в списке и щелкните **Удалить**.

2. Удалите Key Vault с помощью командлета [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об отправке данных задания runbook в рабочую область Log Analytics см. в статье [Пересылка данных задания службы автоматизации Azure в журналы Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Чтобы отслеживать базовые метрики и журналы, воспользуйтесь статьей [Использование оповещения для активации runbook службы автоматизации Azure](automation-create-alert-triggered-runbook.md).
* Сведения об устранении проблем при выполнении операций runbook, см. в статье [Устранение неполадок с последовательностями runbook](./troubleshoot/runbooks.md).