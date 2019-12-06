---
title: Сертификация активов в службе автоматизации Azure
description: Сертификаты безопасно находятся в службе автоматизации Azure, поэтому они могут быть доступны модулям Runbook или конфигурациям DSC для проверки подлинности в Azure и сторонних ресурсах.  В этой статье подробно рассматриваются сертификаты и работа с ними при создании текстовых и графических модулей.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849485"
---
# <a name="certificate-assets-in-azure-automation"></a>Сертификация активов в службе автоматизации Azure

Сертификаты хранятся в службе автоматизации Azure в безопасном режиме, поэтому они могут быть доступны для модулей Runbook или конфигураций DSC с помощью действия **Get-AzureRmAutomationCertificate** для Azure Resource Managerных ресурсов. Эта возможность позволяет создавать модули runbook и конфигурации DSC, которые используют сертификаты для аутентификации или добавляют их в ресурсы Azure либо ресурсы сторонних производителей.

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в хранилище ключей, управляемом системой. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса. Этим процессом управляет служба автоматизации Azure.

## <a name="azurerm-powershell-cmdlets"></a>Командлеты PowerShell для AzureRM

Для AzureRM командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они поставляются как часть [модуля AzureRM. Automation](/powershell/azure/overview), который доступен для использования в модулях Runbook службы автоматизации и конфигурациях DSC.

|Командлеты|Описание|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Извлекает сведения о сертификате для использования в модуле Runbook или в конфигурации DSC. Сам сертификат можно извлечь только с помощью действия Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Создает сертификат в службе автоматизации Azure.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Удаляет сертификат из службы автоматизации Azure.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Задает свойства для существующего сертификата, включая отправку файла сертификата и задание пароля для PFX-файла.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Отправляет сертификат службы в заданную облачную службу.|

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к сертификатам в модуле runbook и конфигурации DSC.

| Действия | Описание |
|:---|:---|
|Get-AutomationCertificate|Получает сертификат для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Не используйте переменные в параметре -Name действия **Get-AutomationCertificate** в runbook или конфигурации DSC, так как это усложняет определение зависимостей между модулями runbook или конфигурацией DSC и переменными службы автоматизации во время разработки.

## <a name="python2-functions"></a>Функции Python2

Функция, приведенная в следующей таблице, используется для доступа к сертификатам в runbook Python2.

| Функция | Описание |
|:---|:---|
| automationassets.get_automation_certificate | Извлекает сведения о ресурсе сертификата. |

> [!NOTE]
> Для доступа к функциям ресурса необходимо импортировать модуль **automationassets** в начало runbook Python.

## <a name="creating-a-new-certificate"></a>Создание нового сертификата

При создании нового сертификата в службу автоматизации Azure передается CER- или PFX-файл. Если пометить сертификат как экспортируемый, его можно будет переместить из хранилища сертификатов службы автоматизации Azure. Если он не является экспортируемым, то его можно использовать только для подписывания в Runbook или конфигурации DSC. Службе автоматизации Azure требуется, чтобы у сертификата был поставщик: **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Создание нового сертификата на портале Azure

1. В учетной записи службы автоматизации щелкните плитку **ресурсы** , чтобы открыть страницу **ресурсы** .
2. Щелкните плитку **Сертификаты** , чтобы открыть страницу **Сертификаты** .
3. Щелкните **Добавить сертификат** в верхней части страницы.
4. Введите имя сертификата в поле **Имя** .
5. Чтобы перейти к CER- или PFX-файлу, щелкните **Выбрать файл** в разделе **Отправка файла сертификата**. Если вы выбрали PFX-файл, укажите пароль и возможность его экспорта.
6. Щелкните **Создать** для сохранения нового ресурса сертификата.

### <a name="to-create-a-new-certificate-with-powershell"></a>Создание нового сертификата с помощью PowerShell

В примере, приведенном ниже, демонстрируется, как создать сертификат службы автоматизации и пометить его как экспортируемый. Вследствие этого импортируется существующий PFX-файл.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Создание нового сертификата с помощью шаблона диспетчер ресурсов

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Использование сертификата

Чтобы использовать сертификат, примените действие **Get-AutomationCertificate**. Нельзя использовать командлет [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) , так как он возвращает сведения о ресурсе сертификата, но не сам сертификат.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

В следующем примере кода показано, как добавить сертификат из Runbook в облачную службу. В этом примере пароль извлекается из зашифрованной переменной службы автоматизации.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Пример графического Runbook

Чтобы добавить команду **Get-AutomationCertificate** в графический модуль Runbook, щелкните сертификат правой кнопкой мыши в области Библиотека и выберите **Добавить на холст**.

![Добавление сертификата на холст](../media/certificates/automation-certificate-add-to-canvas.png)

На следующем рисунке показан пример использования сертификата в графическом Runbook. Это аналогично предыдущему примеру, в котором показано, как добавить сертификат в облачную службу из текстового модуля Runbook.

![Пример графической разработки](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Пример Python2

В следующем примере показано, как получить доступ к сертификатам в модулях runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о работе со связями, с помощью которых можно управлять логическим потоком действий, выполняемых модулем Runbook, см. в [Связи и рабочий процесс](../automation-graphical-authoring-intro.md#links-and-workflow). 
