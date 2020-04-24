---
title: Обеспечение безопасного доступа к Key Vault с помощью пакетной службы
description: Сведения о программном доступе к учетным данным из Key Vault с помощью пакетной службы Azure.
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: d24904c3a539431e8aff420e9fbd8291cddde78a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117460"
---
# <a name="securely-access-key-vault-with-batch"></a>Обеспечение безопасного доступа к Key Vault с помощью пакетной службы

В этой статье вы узнаете, как настроить узлы пакетной службы для безопасного доступа к учетным данным, хранящимся в Azure Key Vault. Нет никакой точки в размещении учетных данных администратора в Key Vault, а затем жестко закодировать учетные данные для доступа к Key Vault из сценария. Решение заключается в использовании сертификата, который предоставляет узлам пакетной службы доступ к Key Vault. С помощью нескольких шагов мы можем реализовать безопасное хранение ключей для пакетной службы.

Для проверки подлинности в Azure Key Vault из узла пакетной службы вам потребуется:

- Учетные данные Azure Active Directory (Azure AD)
- Сертификат
- Учетная запись пакетной службы;
- Пул пакетной службы по крайней мере с одним узлом

## <a name="obtain-a-certificate"></a>Получение сертификата

Если у вас еще нет сертификата, самый простой способ получить его — создать самозаверяющий сертификат с помощью программы `makecert` командной строки.

Обычно можно найти `makecert` по этому пути: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Откройте командную строку от имени администратора и перейдите к `makecert` разделу, используя следующий пример.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Затем используйте `makecert` средство для создания самозаверяющих файлов сертификатов с именами `batchcertificate.cer` и. `batchcertificate.pvk` Общее имя (CN), используемое для этого приложения, не имеет значения, но полезно сделать это с указанием того, для чего предназначен сертификат.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Для `.pfx` пакета требуется файл. Используйте средство [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) `.cer` для преобразования файлов `.pvk` и, созданных `makecert` в одном `.pfx` файле.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Доступ к Key Vault предоставляется либо **пользователю** , либо **субъекту-службе**. Для программного доступа к Key Vault используйте субъект-службу с сертификатом, созданным на предыдущем шаге.

Дополнительные сведения о субъектах-службах Azure см. [в разделе объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Субъект-служба должен находиться в том же клиенте Azure AD, что и Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

URL-адреса приложения не важны, так как они используются только для Key Vaultного доступа.

## <a name="grant-rights-to-key-vault"></a>Предоставление прав Key Vault

Субъекту-службе, созданному на предыдущем шаге, требуется разрешение на извлечение секретов из Key Vault. Разрешение можно предоставить либо с помощью портал Azure, либо с помощью команды PowerShell ниже.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Назначение сертификата учетной записи пакетной службы

Создайте пул пакетной службы, перейдите на вкладку сертификат в пуле и назначьте созданный сертификат. Сертификат теперь находится на всех узлах пакетной службы.

Далее необходимо назначить сертификат учетной записи пакетной службы. Назначение сертификата учетной записи позволяет нам назначить его пулам, а затем узлам. Проще всего это сделать, перейдя к учетной записи пакетной службы на портале, перейдя к разделу **Сертификаты**и выбрав **Добавить**. Отправьте `.pfx` файл, созданный в ходе [получения сертификата](#obtain-a-certificate) , и укажите пароль. По завершении сертификат добавляется в список, и вы можете проверить отпечаток.

Теперь при создании пула пакетной службы можно переходить к **сертификатам** в пуле и назначить сертификат, созданный для этого пула. При этом убедитесь, что для расположения магазина выбрано **хранилище LocalMachine** . Сертификат загружается на все узлы пакетной службы в пуле.

## <a name="install-azure-powershell"></a>Установите Azure PowerShell

Если вы планируете доступ к Key Vault с помощью сценариев PowerShell на узлах, вам потребуется установить библиотеку Azure PowerShell. Существует несколько способов сделать это. Если на узлах установлен Windows Management Framework (WMF) 5, то для его загрузки можно использовать команду Install-Module. Если вы используете узлы, у которых нет WMF 5, проще всего установить файл Azure PowerShell `.msi` с пакетными файлами, а затем вызвать установщик в качестве первой части сценария запуска пакетной службы. Дополнительные сведения см. в этом примере:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Получите доступ к Key Vault.

Теперь все программы настроены для доступа к Key Vault в скриптах, выполняющихся на узлах пакетной службы. Чтобы получить доступ к Key Vault из скрипта, нужно всего лишь выполнить проверку подлинности в Azure AD с помощью сертификата. Чтобы сделать это в PowerShell, используйте приведенные ниже примеры команд. Укажите соответствующий GUID для **отпечатка**, **идентификатор приложения** (идентификатор субъекта-службы) и **идентификатор клиента** (клиента, на котором существует субъект-служба).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

После проверки подлинности доступ к KeyVault обычно осуществляется.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Это учетные данные для использования в скрипте.
