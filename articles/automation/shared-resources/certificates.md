---
title: Управление сертификатами в службе автоматизации Azure
description: Служба автоматизации Azure обеспечивает безопасное хранение сертификатов, чтобы модули Runbook или конфигурации DSC могли обращаться к ним для аутентификации в Azure и сторонних ресурсах. В этой статье описываются сведения о сертификатах и способах работы с ними в текстовой и графической разработке.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2793679fb4588d00ea4e37340b19183398cb9d90
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864323"
---
# <a name="manage-certificates-in-azure-automation"></a>Управление сертификатами в службе автоматизации Azure

Служба автоматизации Azure безопасно хранит сертификаты для доступа к модулям Runbook и конфигурациям DSC с помощью командлета [Get-азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) для ресурсов Azure Resource Manager. Безопасное хранилище сертификатов позволяет создавать модули Runbook и конфигурации DSC, использующие сертификаты для проверки подлинности, или добавлять их в Azure или сторонние ресурсы.

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Служба автоматизации сохраняет ключ в управляемой системой службе Key Vault. Перед сохранением защищенного ресурса Автоматизация загружает ключ из Key Vault, а затем использует его для шифрования ресурса. 

>[!NOTE]
>В этой статье показано, как использовать модуль Azure PowerShell AZ. Вы по-прежнему можете использовать модуль AzureRM. Дополнительные сведения о модуле AZ и совместимости AzureRM см. в разделе [Введение в новый модуль Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации модули можно обновить до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets-to-access-certificates"></a>Командлеты PowerShell для доступа к сертификатам

Командлеты, приведенные в следующей таблице, создают сертификаты службы автоматизации и управляют ими с помощью PowerShell. Они поставляются как часть [модулей AZ](modules.md#az-modules).

|Командлет |Описание|
| --- | ---|
|[Get-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Извлекает сведения о сертификате для использования в модуле Runbook или в конфигурации DSC. Сертификат можно получить только с помощью внутреннего `Get-AutomationCertificate` командлета.|
|[New-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Создает новый сертификат в службе автоматизации.|
|[Remove-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Удаляет сертификат из службы автоматизации.|
|[Set-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Задает свойства для существующего сертификата, включая отправку файла сертификата и задание пароля для **PFX** -файла.|

Командлет [Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) также можно использовать для отправки сертификата службы для указанной облачной службы.

## <a name="internal-cmdlets-to-access-certificates"></a>Внутренние командлеты для доступа к сертификатам

Внутренний командлет, приведенный в следующей таблице, используется для доступа к сертификатам в модулях Runbook. Этот командлет поставляется с глобальным модулем `Orchestrator.AssetManagement.Cmdlets`. Дополнительные сведения см. в разделе [внутренние командлеты](modules.md#internal-cmdlets).

| Внутренний командлет | Описание |
|:---|:---|
|`Get-AutomationCertificate`|Получает сертификат для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Не следует использовать переменные в `Name` параметре `Get-AutomationCertificate` в модуле Runbook или конфигурации DSC. Такие переменные могут усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и переменными автоматизации во время разработки.

## <a name="python-2-functions-to-access-certificates"></a>Функции Python 2 для доступа к сертификатам

Используйте функцию, приведенную в следующей таблице, для доступа к сертификатам в модуле Runbook Python 2.

| Функция | Описание: |
|:---|:---|
| `automationassets.get_automation_certificate` | Извлекает сведения о ресурсе сертификата. |

> [!NOTE]
> Чтобы получить доступ к `automationassets` функциям активов, необходимо импортировать модуль в начале модуля Runbook Python.

## <a name="create-a-new-certificate"></a>Создание нового сертификата

При создании нового сертификата вы отправляете файл. cer или. pfx в службу автоматизации. Если сертификат помечается как экспортируемый, его можно переместить из хранилища сертификатов службы автоматизации. Если он не является экспортируемым, то его можно использовать только для подписывания в Runbook или конфигурации DSC. Служба автоматизации требует, чтобы сертификат имел поставщик **служб шифрования Microsoft Enhanced RSA and AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Создание нового сертификата с портал Azure

1. В учетной записи службы автоматизации выберите **активы** > **Сертификаты** > **Добавить сертификат**.
1. В поле **имя** введите имя сертификата.
1. Чтобы найти файл **CER** или **PFX** , в разделе **отправить файл сертификата**выберите **выбрать файл**. Если вы выберете **PFX** -файл, укажите пароль и укажите, можно ли его экспортировать.
1. Щелкните **создать** , чтобы сохранить новый ресурс сертификата.

### <a name="create-a-new-certificate-with-powershell"></a>Создание нового сертификата с помощью PowerShell

В примере, приведенном ниже, демонстрируется, как создать сертификат службы автоматизации и пометить его как экспортируемый. В этом примере выполняется импорт существующего **PFX** -файла.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Создание нового сертификата с помощью шаблона диспетчер ресурсов

В следующем примере показано, как развернуть сертификат в учетной записи службы автоматизации с помощью шаблона диспетчер ресурсов с помощью PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Получение сертификата

Чтобы получить сертификат, используйте внутренний `Get-AutomationCertificate` командлет. Нельзя использовать командлет [Get-азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , так как он возвращает сведения о ресурсе сертификата, но не сам сертификат.

### <a name="textual-runbook-example"></a>Пример текстового Runbook

В следующем примере показано, как добавить сертификат в облачную службу в модуле Runbook. В этом примере пароль извлекается из зашифрованной переменной службы автоматизации.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Пример графического модуля Runbook

Добавьте действие для внутреннего `Get-AutomationCertificate` командлета в графический модуль Runbook, щелкнув сертификат правой кнопкой мыши в области Библиотека и выбрав **Добавить на холст**.

![Снимок экрана: Добавление сертификата на холст](../media/certificates/automation-certificate-add-to-canvas.png)

На следующем рисунке показан пример использования сертификата в графическом Runbook. 

![Снимок экрана примера графического создания](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Пример Python 2

В следующем примере показано, как получить доступ к сертификатам в модулях Runbook Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о командлетах, используемых для доступа к сертификатам, см. в статье [Управление модулями в службе автоматизации Azure](modules.md).
* Общие сведения о модулях Runbook см. [в статье выполнение Runbook в службе автоматизации Azure](../automation-runbook-execution.md).
* Дополнительные сведения о конфигурациях DSC см. в разделе [Общие сведения о конфигурации состояния](../automation-dsc-overview.md). 
