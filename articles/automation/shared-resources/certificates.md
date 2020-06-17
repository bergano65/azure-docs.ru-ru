---
title: Управление сертификатами в службе автоматизации Azure
description: В этой статье рассказывается, как работать с сертификатами для доступа из runbook и конфигураций DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ebd4bf0ff91342229edb4295489f0cbd0970699a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745029"
---
# <a name="manage-certificates-in-azure-automation"></a>Управление сертификатами в службе автоматизации Azure

Служба автоматизации Azure безопасно хранит сертификаты для доступа к ресурсам Azure Resource Manager из runbook или конфигураций DSC с помощью командлета [Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0). Безопасное хранилище сертификатов позволяет создавать runbook и конфигурации DSC, которые используют эти сертификаты для аутентификации, или добавлять их в ресурсы Azure либо сторонних производителей.

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и сохраняются в службе автоматизации Azure с использованием уникального ключа, который создается для каждой учетной записи службы автоматизации. Служба автоматизации Azure хранит ключ в управляемой системой службе Azure Key Vault. Перед сохранением защищенного ресурса служба автоматизации Azure загружает ключ из Key Vault, а затем использует его для шифрования ресурса. 

## <a name="powershell-cmdlets-to-access-certificates"></a>Командлеты PowerShell для доступа к сертификатам

Представленные в следующей таблице командлеты используются для создания сертификатов службы автоматизации и управления ими с помощью PowerShell. Они поставляются в составе [модулей Az](modules.md#az-modules).

|Командлет |Описание|
| --- | ---|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Извлекает сведения о сертификате для использования в модуле Runbook или в конфигурации DSC. Сам сертификат можно извлечь только с помощью внутреннего командлета `Get-AutomationCertificate`.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Создает новый сертификат в службе автоматизации Azure.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Удаляет сертификат из службы автоматизации Azure.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Задает свойства для существующего сертификата, включая отправку файла сертификата и задание пароля для **PFX-файла**.|

Командлет [Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) также можно использовать для отправки сертификата указанной облачной службы.

## <a name="internal-cmdlets-to-access-certificates"></a>Внутренние командлеты для доступа к сертификатам

Приведенный в следующей таблице внутренний командлет используется для доступа к сертификатам из runbook. Этот командлет поставляется с глобальным модулем `Orchestrator.AssetManagement.Cmdlets`. Дополнительные сведения см. в статье [о внутренних командлетах](modules.md#internal-cmdlets).

| Внутренний командлет | Описание |
|:---|:---|
|`Get-AutomationCertificate`|Получает сертификат для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Не следует использовать переменные в параметре `Name` командлета `Get-AutomationCertificate`, вызываемого из runbook или конфигурации DSC. Такие переменные могут усложнить обнаружение зависимостей между runbook или конфигурациями DSC и переменными службы автоматизации Azure во время разработки.

## <a name="python-2-functions-to-access-certificates"></a>Функции Python 2 для доступа к сертификатам

Функция, приведенная в следующей таблице, используется для доступа к сертификатам из runbook на Python 2.

| Компонент | Описание |
|:---|:---|
| `automationassets.get_automation_certificate` | Извлекает сведения о ресурсе сертификата. |

> [!NOTE]
> Импортируйте модуль `automationassets` в самом начале runbook на Python, чтобы получить доступ к функциям этого ресурса.

## <a name="create-a-new-certificate"></a>Создание сертификата

При создании нового сертификата в службу автоматизации Azure передается CER- или PFX-файл. Пометив сертификат как экспортируемый, вы сможете передавать его из хранилища сертификатов службы автоматизации Azure. Неэкспортируемые сертификаты могут использоваться только для подписи в runbook или конфигурации DSC. Служба автоматизации Azure поддерживает для сертификатов только один поставщик: **поставщик служб шифрования Enhanced RSA и AES корпорации Майкрософт**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Создание нового сертификата на портале Azure

1. В учетной записи службы автоматизации последовательно выберите **Ресурсы** > **Сертификаты** > **Добавить сертификат**.
1. В поле **Имя** введите имя сертификата.
1. Чтобы перейти к **CER-** или **PFX-файлу**, щелкните **Выбрать файл** в разделе **Отправка файла сертификата**. Если вы выберете **PFX-файл**, введите пароль и укажите, разрешен ли экспорт этого сертификата.
1. Щелкните **Создать**, чтобы сохранить новый ресурс сертификата.

### <a name="create-a-new-certificate-with-powershell"></a>Создание нового сертификата с помощью PowerShell

В примере, приведенном ниже, демонстрируется, как создать сертификат службы автоматизации и пометить его как экспортируемый. Этот пример импортирует существующий **PFX-файл**.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Создание нового сертификата с помощью шаблона Resource Manager

Следующий пример демонстрирует, как развернуть сертификат в учетной записи службы автоматизации с помощью шаблона Resource Manager и PowerShell:

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

Чтобы получить сертификат, используйте внутренний командлет `Get-AutomationCertificate`. Вы не сможете использовать командлет [Get-AzureAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0), так как он возвращает сведения о ресурсе сертификата, но не сам сертификат.

### <a name="textual-runbook-example"></a>Пример текстового runbook

Следующий пример кода демонстрирует, как добавить сертификат в облачную службу из runbook. В этом примере пароль извлекается из зашифрованной переменной службы автоматизации.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Пример графического runbook

Добавьте действие для внутреннего командлета `Get-AutomationCertificate` в графический runbook, щелкнув правой кнопкой мыши нужный сертификат в области "Библиотека" и выбрав действие **Добавить на холст**.

![Снимок экрана для процесса добавления сертификата на холст](../media/certificates/automation-certificate-add-to-canvas.png)

На следующем рисунке показан пример использования сертификата в графическом Runbook. 

![Снимок экрана для примера графической разработки](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Пример на Python 2

В следующем примере показано, как получить доступ к сертификатам в runbook на Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о командлетах, используемых для доступа к сертификатам, см. в статье [Управление модулями в службе автоматизации Azure](modules.md).
* Общие сведения о runbook см. в статье [Выполнение модуля Runbook в службе автоматизации Azure](../automation-runbook-execution.md).
* Дополнительные сведения о конфигурациях DSC см. в статье [Обзор службы State Configuration службы автоматизации Azure](../automation-dsc-overview.md). 

