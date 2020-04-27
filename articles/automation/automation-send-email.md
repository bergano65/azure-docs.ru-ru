---
title: Отправка электронной почты из модуля Runbook службы автоматизации Azure
description: Узнайте, как использовать SendGrid для отправки электронной почты из Runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604785"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Руководство по Отправка электронной почты из модуля Runbook службы автоматизации Azure

Сообщение электронной почты из модуля Runbook можно отправить с помощью [SendGrid](https://sendgrid.com/solutions), используя PowerShell. В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Создайте хранилище ключей Azure.
> * Сохранение ключа API `SendGrid` в хранилище ключей.
> * Создание повторно используемого модуля runbook, который извлекает ключ API и отправляет сообщение электронной почты с использованием ключа API, хранящегося в [Azure Key Vault](/azure/key-vault/).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены необходимые условия для выполнения инструкций из этого руководства.

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Создайте учетную запись SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Автоматизируйте пользовательскую документацию Azure](automation-offering-get-started.md) с модулями **Az** и [управляйте учетными записями запуска от имени службы автоматизации Azure](automation-create-runas-account.md) для хранения и выполнения модуля Runbook.

## <a name="create-an-azure-key-vault"></a>создать Azure Key Vault;

Экземпляр Azure Key Vault можно создать, используя описанный ниже скрипт PowerShell. Замените значения переменной, выделенные курсивом, значениями для конкретной среды. Используйте внедренный компонент Azure Cloud Shell с помощью кнопки **Попробуйте!** , расположенной в правом верхнем углу блока кода. Также можно скопировать и запустить код локально, если на локальном компьютере установлен [Модуль PowerShell Azure ](/powershell/azure/install-az-ps).

> [!NOTE]
> Чтобы получить ключ API, необходимо выполнить действия, описанные в статье [Как найти ключ API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

## <a name="import-required-modules-to-your-automation-account"></a>Импорт необходимых модулей в учетную запись службы автоматизации

Чтобы использовать Azure Key Vault в модуле runbook, учетной записи автоматизации нужны следующие модули:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile);
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Щелкните **Deploy to Azure Automation** (Развернуть в службе автоматизации Azure) на вкладке "Служба автоматизации Azure" в разделе **Параметры установки**. Портал Azure можно открыть с помощью этого действия. На странице "Импорт" выберите учетную запись службы автоматизации и нажмите кнопку **ОК**.

Дополнительные методы для добавления необходимых модулей см. в статье [Импорт модулей](/azure/automation/shared-resources/modules#importing-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Создание модуля Runbook для отправки электронной почты

После того, как вы создали хранилище ключей и сохранили свой ключ API `SendGrid`, необходимо создать модуль runbook, с помощью которого можно извлечь ключ API и отправить электронное письмо.

Этот модуль runbook будет использовать `AzureRunAsConnection` в качестве [учетной записи запуска от имени](automation-create-runas-account.md) для аутентификации в Azure, чтобы получить секрет из Azure Key Vault.

Используйте этот пример для создания модуля Runbookс именем **Send-GridMailMessage**. Можно изменить сценарий PowerShell и повторно использовать его для разных сценариев.

1. Перейдите к своей учетной записи службы автоматизации Azure.
2. В разделе **Автоматизация процессов** щелкните **Модули Runbook**.
3. В верхней части списка модулей runbook выберите **+ Создать Runbook**.
4. На странице **Добавление модуля Runbook** введите **Send-GridMailMessage** в качестве имени модуля Runbook. Для типа runbook выберите значение **PowerShell**. Затем выберите **Создать**.
   ![Создание модуля Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Это действие создает новый runbook и открывает страницу **Изменение Runbook PowerShell**.
   ![Вносит изменения в модуль Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Скопируйте следующий пример PowerShell на страницу **Изменение**. Убедитесь, что `$VaultName` — это имя, указанное при создании хранилища ключей.

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

Чтобы убедиться, что модуль Runbook выполняется успешно, выполните действия, описанные в статье [Тестирование модуля Runbook](manage-runbooks.md#testing-a-runbook) или [Start a runbook in Azure Automation](start-runbooks.md) (Запуск модуля Runbook в службе автоматизации Azure).
Если тестовое сообщение электронной почты не видно изначально, проверьте папки **Нежелательная почта** и **Спам**.

## <a name="clean-up"></a>Очистка

Удалите runbook, если он больше не нужен. Для этого выберите ненужный runbook в списке и щелкните **Удалить**.

Удалите хранилище ключей с помощью командлета [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о проблемах, возникающих при создании или запуске модуля Runbook, см. в разделе [Troubleshoot errors with runbooks](./troubleshoot/runbooks.md) (Устранение ошибок с помощью модулей Runbook).
* Чтобы обновить модули в своей учетной записи службы автоматизации, см. статью [Как обновить модули Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).
* Чтобы отслеживать выполнение модуля Runbook, см. статью [Forward job status and job streams from Automation to Azure Monitor logs](automation-manage-send-joblogs-log-analytics.md) (Переадресация состояния работы и потоков работ из службы автоматизации в журналы Azure Monitor).
* Чтобы вызвать модуль Runbook с помощью предупреждения, см. статью [Use an alert to trigger an Azure Automation runbook](automation-create-alert-triggered-runbook.md) (Использование предупреждения для запуска модуля Runbook службы автоматизации Azure).
