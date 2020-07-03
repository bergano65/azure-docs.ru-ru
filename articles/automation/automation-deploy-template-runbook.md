---
title: Развертывание шаблона Azure Resource Manager в runbook службы автоматизации Azure
description: Как развернуть шаблон Azure Resource Manager, хранящийся в службе хранилища Azure, из runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, служба автоматизации azure
ms.openlocfilehash: 1bf381499ac31fafc8aaeef2b4ee488cfa1aa5c1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994684"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Развертывание шаблона Azure Resource Manager в runbook PowerShell службы автоматизации Azure

Можно написать [runbook PowerShell службы автоматизации Azure](automation-first-runbook-textual-powershell.md), который развертывает ресурс Azure с помощью [шаблона управления ресурсами Azure](../azure-resource-manager/resource-manager-create-first-template.md).

Таким образом можно автоматизировать развертывание ресурсов Azure. Можно хранить шаблоны Resource Manager в центральном безопасном сетевом расположении, например в службе хранилища Azure.

В этой статье мы создадим runbook PowerShell, использующий шаблон Resource Manager, который расположен в [службе хранилища Azure](../storage/common/storage-introduction.md), для развертывания новой учетной записи хранения Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется следующее.

* Подписка Azure. Если у вас ее еще нет, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться для использования бесплатной учетной записи](https://azure.microsoft.com/free/).
* [Учетная запись службы автоматизации](automation-sec-configure-azure-runas-account.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure.  Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* [Учетная запись хранения Azure](../storage/common/storage-create-storage-account.md) для хранения шаблона Resource Manager.
* Azure PowerShell, установленные на локальном компьютере. Сведения о том, как получить Azure PowerShell, см. [в разделе Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) .

## <a name="create-the-resource-manager-template"></a>Создание шаблона Resource Manager

В этом примере мы используем шаблон Resource Manager, который развертывает новую учетную запись хранения Azure.

Скопируйте приведенный ниже текст в текстовый редактор.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Сохраните файл локально как **темплатетест. JSON**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Сохранение шаблона Resource Manager в службе хранилища Azure

Теперь мы используем PowerShell для создания общей папки службы хранилища Azure и отправки файла **темплатетест. JSON** .
Инструкции по созданию файлового ресурса и передаче файла на портал Azure см. в разделе [Приступая к работе с хранилищем файлов Azure в Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Запустите PowerShell на локальном компьютере и выполните приведенные команды, чтобы создать файловый ресурс и передать в него шаблон Resource Manager.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Создание сценария runbook PowerShell

Теперь создадим сценарий PowerShell, который получает файл **темплатетест. JSON** из службы хранилища Azure и развертывает шаблон для создания новой учетной записи хранения Azure.

Вставьте приведенный ниже текст в текстовый редактор.

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Сохраните файл локально как **деплойтемплате. ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Импорт runbook в учетную запись службы автоматизации Azure и его публикация

Теперь мы используем PowerShell, чтобы импортировать runbook в учетную запись службы автоматизации Azure, а затем опубликовать его. Сведения о том, как импортировать и опубликовать runbook на портале Azure, см. в статье [Управление модулями runbook в службе автоматизации Azure](manage-runbooks.md).

Чтобы импортировать **деплойтемплате. ps1** в учетную запись службы автоматизации в качестве модуля Runbook PowerShell, выполните следующие команды PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Запуск модуля runbook

Теперь запустите модуль Runbook, вызвав командлет [Start-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) . Сведения о том, как запустить runbook на портале Azure, см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

В консоли PowerShell выполните следующие команды.

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Запустится runbook. Его состояние можно проверить, выполнив команду `$job.Status`.

Модуль runbook получает шаблон Resource Manager и использует его для развертывания новой учетной записи хранения Azure.
Вы увидите, что учетная запись хранения создана, выполнив следующую команду.

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Сводка

Вот и все! Теперь вы можете использовать службу автоматизации Azure и службу хранилища Azure с шаблонами диспетчер ресурсов для развертывания всех ресурсов Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о диспетчер ресурсов шаблонах см. в разделе [Общие сведения о Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Чтобы приступить к работе со службой хранилища Azure, изучите раздел [Введение в хранилище Microsoft Azure](../storage/common/storage-introduction.md).
* Другие полезные runbook службы автоматизации Azure приведены в разделе [Коллекции модулей Runbook и других модулей для службы автоматизации Azure](automation-runbook-gallery.md).
* Чтобы найти другие полезные шаблоны диспетчер ресурсов, ознакомьтесь с [шаблонами](https://azure.microsoft.com/resources/templates/)быстрого запуска Azure.
* Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
