---
title: Управление сертификатом в автоматизации Azure
description: Сертификаты надежно хранятся в Azure Automation, чтобы запуски или конфигурации DSC могли получить к ним доступ к аутентификации в отношении ресурсов Azure и сторонних сторонних групп. В этой статье подробно рассматриваются сертификаты и работа с ними при создании текстовых и графических модулей.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732830"
---
# <a name="manage-certificates-in-azure-automation"></a>Управление сертификатами в автоматизации Azure

Сертификаты надежно хранятся в Azure Automation, чтобы к ним можно было получить доступ в запущенных книгах или конфигурациях DSC с помощью деятельности [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) для ресурсов Azure Resource Manager. Безопасное хранилище сертификатов позволяет создавать runbooks и DSC конфигурации, которые используют сертификаты для проверки подлинности или добавить их в Azure или сторонних ресурсов.

Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в управляемом системой Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса. Этим процессом управляет служба автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="az-powershell-cmdlets"></a>Az PowerShell cmdlets

Для Az cmdlets в следующей таблице используются для создания и управления учетными данными автоматизации с windows PowerShell. Они погрузки в рамках [модуля Az.Automation](/powershell/azure/overview), который доступен для использования в автоматизации runbooks и DSC конфигураций.

|Командлет |Описание|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Отправляет сертификат службы в заданную облачную службу.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Извлекает сведения о сертификате для использования в модуле Runbook или в конфигурации DSC. Вы можете получить сертификат только `Get-AutomationCertificate` с помощью действия.|
|[Новый АзАвтоматизацияСертификат](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Создает новый сертификат в Azure Automation.|
|[Удалить-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Удаляет сертификат из службы автоматизации Azure.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Устанавливает свойства для существующего сертификата, включая загрузку файла сертификата и настройку пароля для файла **.pfx.**|

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к сертификатам в модуле runbook и конфигурации DSC.

| Действия | Описание |
|:---|:---|
|`Get-AutomationCertificate`|Получает сертификат для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Следует избегать использования переменных в параметре `Name` в конфигурации `Get-AutomationCertificate` runbook или DSC. Использование переменных в этом параметре усложняет обнаружение зависимостей между конфигурациями runbooks или DSC и переменными автоматизации во время проектирования.

## <a name="python-2-functions"></a>Функции Python 2

Функция в следующей таблице используется для доступа к сертификатам в ступеньке Python 2.

| Компонент | Описание |
|:---|:---|
| `automationassets.get_automation_certificate` | Извлекает сведения о ресурсе сертификата. |

> [!NOTE]
> Для доступа `automationassets` к функциям актива необходимо импортировать модуль в начале выполнения программы Python.

## <a name="creating-a-new-certificate"></a>Создание нового сертификата

При создании нового сертификата в службу автоматизации Azure передается CER- или PFX-файл. Если пометить сертификат как экспортируемый, его можно будет переместить из хранилища сертификатов службы автоматизации Azure. Если он не экспортируется, то он может быть использован только для подписания в конфигурации runbook или DSC. Azure Automation требует, чтобы сертификат был у провайдера **Microsoft Enhanced RSA и Cryptographic Provider AES.**

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Создание нового сертификата на портале Azure

1. С вашей учетной записи Автоматизация щелкните **Активы,** чтобы открыть страницу Активов.
2. Выберите **Сертификаты,** чтобы открыть страницу Сертификатов.
3. Нажмите **Добавить сертификат** в верхней части страницы.
4. Введите имя для сертификата в поле **«Имя».**
5. Чтобы просмотреть файл **.cer** или **.pfx,** нажмите **Выберите файл** под **загрузить файл сертификата.** Если вы выберете файл **.pfx,** укажите пароль и укажите, можно ли его экспортировать.
6. Щелкните **Создать** для сохранения нового ресурса сертификата.

### <a name="create-a-new-certificate-with-powershell"></a>Создание нового сертификата с PowerShell

В примере, приведенном ниже, демонстрируется, как создать сертификат службы автоматизации и пометить его как экспортируемый. Этот пример импортирует существующий файл **.pfx.**

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Создание нового сертификата с шаблоном «Менеджер ресурсов»

Ниже приводится следующий пример, как развернуть сертификат в учетную запись автоматизации с помощью шаблона «Менеджер ресурсов» через PowerShell:

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

Чтобы использовать сертификат, `Get-AutomationCertificate` используйте действие. Вы не можете использовать [cmdlet Get-AzAutomationCertificate,](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) так как он возвращает информацию об активе сертификата, но не сам сертификат.

### <a name="textual-runbook-example"></a>Пример текстового запуска

В следующем примере показано, как добавить сертификат в облачную службу в службу runbook. В этом примере пароль извлекается из зашифрованной переменной службы автоматизации.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Графический пример runbook

Добавьте `Get-AutomationCertificate` действие в графический runbook, нажав правой кнопкой на сертификат в панели библиотеки и выбрав **Добавить в холст.**

![Добавление сертификата на холст](../media/certificates/automation-certificate-add-to-canvas.png)

На следующем рисунке показан пример использования сертификата в графическом Runbook. Это то же самое, что и предыдущий пример, в ходе которого показано, как добавить сертификат в облачную службу из текстовой службы.

![Пример графической разработки](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 пример

В следующем примере показано, как получить доступ к сертификатам в runbooks Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Следующие шаги

- Чтобы узнать больше о работе со ссылками для управления логическим потоком действий, выполняемых вашего runbook, [см.](../automation-graphical-authoring-intro.md#links-and-workflow) 
