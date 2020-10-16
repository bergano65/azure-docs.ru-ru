---
title: Обеспечение безопасного доступа к Key Vault с помощью пакетной службы
description: Сведения о программном доступе к учетным данным в Key Vault с помощью пакетной службы Azure.
ms.topic: how-to
ms.date: 02/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6938d0fcd2357efcf03053b0c9b2bde3954270b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079444"
---
# <a name="securely-access-key-vault-with-batch"></a>Обеспечение безопасного доступа к Key Vault с помощью пакетной службы

Из этой статьи вы узнаете, как настроить узлы пакетной службы для безопасного доступа к учетным данным, хранимым в Azure Key Vault. Нет никакого смысла помещать учетные данные администратора в Key Vault, если сведения для доступа к этому Key Vault будут жестко запрограммированы прямо в коде скрипта. Правильное решение — использовать сертификат, который предоставляет узлам пакетной службы доступ к Key Vault. Всего за несколько шагов мы можем реализовать безопасное хранилище ключей для пакетной службы.

Чтобы пройти аутентификацию в Azure Key Vault с узла пакетной службы, вам потребуется следующее:

- учетные данные Azure Active Directory (Azure AD);
- сертификат;
- учетная запись пакетной службы;
- пул пакетной службы хотя бы с одним узлом.

## <a name="obtain-a-certificate"></a>Получение сертификата

Если у вас еще нет сертификата, проще всего будет создать самозаверяющий сертификат с помощью программы командной строки `makecert`.

Обычно `makecert` размещается в папке `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Откройте командную строку с правами администратора и перейдите к папке `makecert`, как показано в следующем примере.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Затем с помощью средства `makecert` создайте файлы самозаверяющего сертификата `batchcertificate.cer` и `batchcertificate.pvk`. Общее имя сертификата для нашего примера не имеет значения, но лучше присваивать такое имя, которое будет напоминать о назначении сертификата.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Для пакетной службы требуется файл `.pfx`. С помощью средства [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) преобразуйте файлы `.cer` и `.pvk`, созданные `makecert`, в единый файл `.pfx`.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Доступ к Key Vault предоставляется для **пользователя** или **субъекта-службы**. Для программного доступа к Key Vault нужно использовать субъект-службу с сертификатом, который мы создали на предыдущем шаге.

Дополнительные сведения о субъектах-службах Azure см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Субъект-служба должен размещаться в том же клиенте Azure AD, что и Key Vault.

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

URL-адреса приложения не имеют значения, так как мы используем их только для доступа к Key Vault.

## <a name="grant-rights-to-key-vault"></a>Предоставление доступа к Key Vault

Субъекту-службе, созданному на предыдущем шаге, нужны разрешения на получение секретов из Key Vault. Предоставить такое разрешение можно на портале Azure либо с помощью приведенной ниже команды PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Назначение сертификата для учетной записи пакетной службы

Создайте пул пакетной службы, перейдите на вкладку сертификата в области со сведениями об этом пуле и назначьте ему только что созданный сертификат. Теперь этот сертификат будет размещен на всех узлах пакетной службы.

Затем нам нужно назначить этот сертификат учетной записи пакетной службы. Назначив сертификат учетной записи, вы сможете назначить его пулам, а затем и узлам. Проще всего для этого перейти к учетной записи пакетной службы на портале и в разделе **Сертификаты** выбрать действие **Добавить**. Отправьте файл `.pfx`, который мы создали в разделе [Получение сертификата](#obtain-a-certificate), и укажите пароль. Когда этот процесс завершится, сертификат появится в списке и вы сможете проверить отпечаток.

Теперь при создании пула пакетной службы вы можете открыть вкладку **Сертификаты** для этого пула и назначить ему только что созданный сертификат. При этом убедитесь, что выбрано расположение хранилища **LocalMachine**. Сертификат отправляется на все узлы пакетной службы в пуле.

## <a name="install-azure-powershell"></a>Установите Azure PowerShell

Если вы планируете обращаться к Key Vault через скрипты PowerShell на узлах, потребуется установить библиотеку Azure PowerShell. Для этого есть несколько способов, например с помощью команды install-module службы Windows Management Framework (WMF) 5, если она установлена на этих узлах. Если на ваших узлах нет WMF 5, для установки библиотеки проще всего объединить файл `.msi` Azure PowerShell с файлами пакетной службы, а затем вызвать установщик в самом начале скрипта запуска пакетной службы. Этот вариант подробно представлен в следующем примере:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Получите доступ к Key Vault.

Теперь все готово для работы с Key Vault из скриптов, запущенных на узлах пакетной службы. Чтобы обратиться к Key Vault из скрипта, нужно лишь пройти проверку подлинности в Azure AD с использованием сертификата. Для этого выполните приведенные ниже команды PowerShell. Укажите правильный GUID для параметра **отпечатка**, а также **идентификатор приложения** и **идентификатор клиента** (клиент, в котором размещается этот субъект-служба).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

После проверки подлинности обращайтесь к KeyVault обычным образом.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Это учетные данные для использования в скрипте.
