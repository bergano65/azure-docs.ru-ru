---
title: Управление сертификатом в службе автоматизации Azure
description: Сертификаты безопасно хранятся в службе автоматизации Azure, поэтому модули Runbook или конфигурации DSC могут получить к ним доступ для аутентификации в Azure и сторонних ресурсах. В этой статье подробно рассматриваются сертификаты и работа с ними при создании текстовых и графических модулей.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732830"
---
# <a name="manage-certificates-in-azure-automation"></a>Управление сертификатами в службе автоматизации Azure

Сертификаты хранятся в службе автоматизации Azure в безопасном режиме, поэтому они могут быть доступны для модулей Runbook или конфигураций DSC с помощью действия [Get-азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) для Azure Resource Managerных ресурсов. Безопасное хранилище сертификатов позволяет создавать модули Runbook и конфигурации DSC, использующие сертификаты для проверки подлинности, или добавлять их в Azure или сторонние ресурсы.

Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в управляемом системой Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса. Этим процессом управляет служба автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>AZ PowerShell командлеты

Для AZ командлеты, приведенные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они поставляются в составе [модуля AZ. Automation](/powershell/azure/overview), который доступен для использования в модулях Runbook службы автоматизации и конфигурациях DSC.

|Командлет |Описание|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Отправляет сертификат службы в заданную облачную службу.|
|[Get-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Извлекает сведения о сертификате для использования в модуле Runbook или в конфигурации DSC. Сертификат можно получить только с помощью `Get-AutomationCertificate` действия.|
|[New-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Создает новый сертификат в службе автоматизации Azure.|
|[Remove-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Удаляет сертификат из службы автоматизации Azure.|
|[Set-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Задает свойства для существующего сертификата, включая отправку файла сертификата и задание пароля для **PFX** -файла.|

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к сертификатам в модуле runbook и конфигурации DSC.

| Действия | Описание |
|:---|:---|
|`Get-AutomationCertificate`|Получает сертификат для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Не следует использовать переменные в `Name` параметре `Get-AutomationCertificate` в модуле Runbook или конфигурации DSC. Использование переменных в этом параметре усложняет обнаружение зависимостей между модулями Runbook или конфигурациями DSC и переменными автоматизации во время разработки.

## <a name="python-2-functions"></a>Функции Python 2

Функция, приведенная в следующей таблице, используется для доступа к сертификатам в модуле Runbook Python 2.

| Функция | Описание |
|:---|:---|
| `automationassets.get_automation_certificate` | Извлекает сведения о ресурсе сертификата. |

> [!NOTE]
> Чтобы получить доступ к `automationassets` функциям активов, необходимо импортировать модуль в начале модуля Runbook Python.

## <a name="creating-a-new-certificate"></a>Создание нового сертификата

При создании нового сертификата в службу автоматизации Azure передается CER- или PFX-файл. Если пометить сертификат как экспортируемый, его можно будет переместить из хранилища сертификатов службы автоматизации Azure. Если он не является экспортируемым, то его можно использовать только для подписывания в Runbook или конфигурации DSC. Службе автоматизации Azure требуется, чтобы сертификат имел поставщик **криптографических поставщиков Microsoft Enhanced RSA и AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Создание нового сертификата с портал Azure

1. В учетной записи службы автоматизации щелкните **активы** , чтобы открыть страницу ресурсы.
2. Выберите **Сертификаты** , чтобы открыть страницу сертификаты.
3. Щелкните **Добавить сертификат** в верхней части страницы.
4. Введите имя сертификата в поле **имя** .
5. Чтобы найти файл **CER** или **PFX** , щелкните **выбрать файл** в разделе **отправить файл сертификата**. Если вы выберете **PFX**-файл, укажите пароль и укажите, можно ли его экспортировать.
6. Щелкните **Создать** для сохранения нового ресурса сертификата.

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

## <a name="using-a-certificate"></a>Использование сертификата

Чтобы использовать сертификат, используйте `Get-AutomationCertificate` действие. Нельзя использовать командлет [Get-азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , так как он возвращает сведения о ресурсе сертификата, но не сам сертификат.

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

Добавьте `Get-AutomationCertificate` действие в графический модуль Runbook, щелкнув сертификат правой кнопкой мыши в области Библиотека и выбрав **Добавить на холст**.

![Добавление сертификата на холст](../media/certificates/automation-certificate-add-to-canvas.png)

На следующем рисунке показан пример использования сертификата в графическом Runbook. Это аналогично предыдущему примеру, в котором показано, как добавить сертификат в облачную службу из текстового модуля Runbook.

![Пример графической разработки](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Пример Python 2

В следующем примере показано, как получить доступ к сертификатам в модулях Runbook python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о работе со ссылками для управления логическим потоком действий, выполняемых модулем Runbook, см. [в разделе ссылки в графической разработке](../automation-graphical-authoring-intro.md#links-and-workflow). 
