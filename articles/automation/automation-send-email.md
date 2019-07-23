---
title: Отправка электронной почты из модуля Runbook службы автоматизации Azure
description: Узнайте, как использовать SendGrid для отправки электронной почты из Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235095"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Руководство по Отправка электронной почты из модуля Runbook службы автоматизации Azure

Сообщение электронной почты из модуля Runbook можно отправить с помощью [SendGrid](https://sendgrid.com/solutions), используя PowerShell. В этом руководстве показано, как создать повторно используемый модуль Runbook, который отправляет электронную почту с использованием ключа API, хранящегося в [хранилище ключей Azure](/azure/key-vault/).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
>
> * создавать хранилище ключей Azure;
> * сохранять свой ключ API SendGrid в хранилище ключей;
> * создавать модуль Runbook, который получает ключ API и отправляет электронную почту.

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены необходимые условия для выполнения инструкций из этого руководства.

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Создайте учетную запись SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Автоматизируйте пользовательскую документацию Azure](automation-offering-get-started.md) с модулями **Az** и [управляйте учетными записями запуска от имени службы автоматизации Azure](automation-create-runas-account.md) для хранения и выполнения модуля Runbook.

## <a name="create-an-azure-keyvault"></a>Создание хранилища ключей Azure

Хранилище ключей Azure можно создать, используя следующий сценарий PowerShell. Замените значения переменной, выделенные курсивом, значениями для конкретной среды. Используйте внедренный компонент Azure Cloud Shell с помощью кнопки <kbd>Попробуйте!</kbd>, расположенной в правом верхнем углу блока кода. Также можно скопировать и запустить код локально, если на локальном компьютере установлен [Модуль PowerShell Azure ](/powershell/azure/install-az-ps).

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

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Другие способы создания хранилища ключей Azure и сохранения секрета см. в статье [Key Vault Documentation](/azure/key-vault/) (Документация хранилища ключей).

## <a name="import-required-modules-to-your-automation-account"></a>Импорт необходимых модулей в учетную запись службы автоматизации

Чтобы использовать хранилище ключей Azure в модуле Runbook, учетной записи автоматизации понадобятся следующие модули:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Щелкните <kbd>Развернуть в службе автоматизации Azure</kbd> на вкладке "Служба автоматизации Azure" в разделе "Параметры установки". Портал Azure можно открыть с помощью этого действия. На странице импорта выберите учетную запись службы автоматизации и нажмите кнопку <kbd>ОК</kbd>.

Дополнительные методы для добавления необходимых модулей см. в статье [Импорт модулей](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Создание модуля Runbook для отправки электронной почты

После того, как вы создали хранилище ключей и сохранили свой ключ API SendGrid, необходимо создать модуль Runbook, с помощью которого можно извлечь ключ API и отправить электронную почту.

Этот модуль Runbook будет использовать [Manage Azure Automation Run As accounts](automation-create-runas-account.md) ("Управление учетными записями запуска от имени службы автоматизации Azure") AzureRunAsConnection для аутентификации в Azure, чтобы получить секрет в хранилище ключей Azure.

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

Чтобы убедиться, что модуль Runbook выполняется успешно, выполните действия, описанные в статье [Тестирование модуля Runbook](manage-runbooks.md#test-a-runbook) или [Start a runbook in Azure Automation](start-runbooks.md) (Запуск модуля Runbook в службе автоматизации Azure).
Если тестовое сообщение электронной почты не видно изначально, проверьте папки **Нежелательная почта** и **Спам**.

## <a name="clean-up"></a>Очистка

Удалите runbook, если он больше не нужен. Для этого выберите ненужный runbook в списке и щелкните **Удалить**.

Удалите хранилище ключей с помощью командлета [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Дополнительная информация

* Сведения о проблемах, возникающих при создании или запуске модуля Runbook, см. в разделе [Troubleshoot errors with runbooks](./troubleshoot/runbooks.md) (Устранение ошибок с помощью модулей Runbook).
* Чтобы обновить модули в своей учетной записи службы автоматизации, см. статью [How to update Azure PowerShell modules in Azure Automation](automation-update-azure-modules.md) (Как обновить модули Azure PowerShell в службе автоматизации Azure).
* Чтобы отслеживать выполнение модуля Runbook, см. статью [Forward job status and job streams from Automation to Azure Monitor logs](automation-manage-send-joblogs-log-analytics.md) (Переадресация состояния работы и потоков работ из службы автоматизации в журналы Azure Monitor).
* Чтобы вызвать модуль Runbook с помощью предупреждения, см. статью [Use an alert to trigger an Azure Automation runbook](automation-create-alert-triggered-runbook.md) (Использование предупреждения для запуска модуля Runbook службы автоматизации Azure).
