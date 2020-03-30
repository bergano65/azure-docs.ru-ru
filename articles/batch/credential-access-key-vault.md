---
title: Безопасный доступ к Key Vault с пакетом - Azure Batch
description: Узнайте, как запрограммировать доступ к учетным данным из Key Vault с помощью Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192308"
---
# <a name="securely-access-key-vault-with-batch"></a>Обеспечение безопасного доступа к Key Vault с помощью пакетной службы

В этой статье вы узнаете, как настроить узлы пакета для безопасного доступа к учетным данным, хранящимся в Хранилище ключей Azure. Нет смысла вводить учетные данные в Key Vault, а затем в ключевые учетные данные для доступа к Key Vault из сценария. Решение заключается в использовании сертификата, который предоставляет узлам пакета доступа к Key Vault. С помощью нескольких шагов мы можем реализовать безопасное хранилище ключей для пакета.

Для проверки подлинности в Хранилище ключей Azure из узла пакета необходимо:

- Учетные данные Active Directory Azure (Azure AD)
- Сертификат
- Учетная запись пакета
- Пул пакетов с по крайней мере одним узлам

## <a name="obtain-a-certificate"></a>Получение сертификата

Если у вас еще нет сертификата, самый простой способ получить его `makecert` — создать сертификат самостоятельного подписания с помощью инструмента командной строки.

Обычно вы `makecert` можете найти `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`на этом пути: . Откройте запрос команды в качестве `makecert` администратора и перейдите к следующему примеру.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Затем используйте `makecert` инструмент для создания вызванных `batchcertificate.cer` `batchcertificate.pvk`и . Используемое общее имя (CN) не является важным для этого приложения, но полезно сделать его чем-то, что говорит вам, для чего используется сертификат.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Пакет требует `.pfx` файла. Используйте инструмент [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) `.cer` для `.pvk` преобразования `makecert` файлов, `.pfx` созданных в один файл.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Доступ к Key Vault предоставляется либо **пользователю,** либо **директору службы.** Чтобы получить доступ к Key Vault программно, используйте директор службы с сертификатом, который мы создали предыдущий шаг.

Более подробную информацию о принципах службы Azure можно узнать [в каталоге Active](../active-directory/develop/app-objects-and-service-principals.md)

> [!NOTE]
> Основной сервис должен быть в том же арендаторе Azure AD, что и Key Vault.

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

URL-адреса для приложения не важны, так как мы используем их только для доступа key Vault.

## <a name="grant-rights-to-key-vault"></a>Права гранта на Key Vault

Директору службы, созданного на предыдущем этапе, требуется разрешение на извлечение секретов из Key Vault. Разрешение может быть предоставлено либо через портал Azure, либо с командой PowerShell ниже.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Назначить сертификат учетной записи пакета

Создайте пул пакетов, затем перейдите на вкладку сертификата в пуле и назначайте созданный сертификат. Сертификат теперь находится на всех узлах пакета.

Далее нам нужно присвоить сертификат учетной записи Пакета. Назначение сертификата на счет позволяет нам присвоить его пулам, а затем узлам. Самый простой способ сделать это — зайти на свой аккаунт Batch на портале, перейти к **Сертификатам**и выбрать **Добавить.** Загрузите файл, `.pfx` [созданный в сертификате Get,](#obtain-a-certificate) и предоставите пароль. После завершения, сертификат добавляется в список, и вы можете проверить отпечаток пальца.

Теперь, когда вы создаете пул пакетов, можно сделать переход к **сертификатам** в пуле и назначить созданный сертификат в этот пул. При этом убедитесь, что вы выберете **LocalMachine** для расположения магазина. Сертификат загружается на все узлы пакета в пуле.

## <a name="install-azure-powershell"></a>Установите Azure PowerShell

Если вы планируете получить доступ к Key Vault с помощью скриптов PowerShell на узлах, то вам нужна установлена библиотека Azure PowerShell. Есть несколько способов сделать это, если у узлы установлены рамки управления Windows (WMF) 5, то вы можете использовать команду установки модуля, чтобы загрузить его. Если вы используете узлы, которые не имеют WMF 5, самый простой способ `.msi` установить его состоит в том, чтобы связать файл Azure PowerShell с файлами пакета, а затем вызвать установщика в качестве первой части сценария запуска пакета. Смотрите этот пример для получения подробной информации:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Получите доступ к Key Vault.

Теперь мы все настроены для доступа к Key Vault в скриптах, работающих на узлах пакета. Чтобы получить доступ к Key Vault из скрипта, все, что вам нужно, это чтобы ваш скрипт был проверен на Azure AD с помощью сертификата. Для этого в PowerShell используйте следующие примеры команд. Укажите соответствующий GUID для **Thumbprint,** **Идентификатор приложения** (идентификатор вашего основного обслуживания) и **идентификатор арендатора** (арендатор, где существует ваш основной сервис).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

После проверки подлинности, доступ к KeyVault, как обычно.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Это учетные данные для использования в скрипте.
