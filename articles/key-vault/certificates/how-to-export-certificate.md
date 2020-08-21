---
title: Экспорт сертификата из Azure Key Vault
description: Экспорт сертификата из Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588898"
---
# <a name="export-certificate-from-azure-key-vault"></a>Экспорт сертификата из Azure Key Vault

Azure Key Vault позволяет легко подготавливать, администрировать и развертывать цифровые сертификаты для сети и поддерживать безопасный обмен данными между приложениями. Общую информацию о сертификатах см. в статье [Сведения о сертификатах Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

## <a name="about-azure-key-vault-certificate"></a>О сертификате Azure Key Vault

### <a name="composition-of-certificate"></a>Создание сертификата
Когда создается сертификат Key Vault, адресуемые ключ и секрет также создаются с тем же именем. Ключ Key Vault разрешает операции с ключами, а секрет Key Vault позволяет извлечь значение сертификата в виде секрета. Сертификат Key Vault также содержит открытые метаданные сертификата x509. [Дополнительные сведения](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Доступные и недоступные для экспорта ключи
Созданный сертификат Key Vault можно извлечь из адресуемого секрета с помощью закрытого ключа в формате PFX или PEM. Политика, используемая для создания сертификата, указывает, что ключ доступен для экспорта. Если политика указывает, что ключ недоступен для экспорта, тогда закрытый ключ не будет частью значения при извлечении в качестве секрета.

С сертификатом поддерживается два типа ключей: RSA или RSA HSM. Для экспорта доступен только ключ RSA, но не RSA HSM. [Дополнительные сведения](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Хранимый в Azure Key Vault сертификат можно экспортировать с помощью Azure CLI, PowerShell или портала Azure.

> [!NOTE]
> Важно отметить, что при импорте сертификата в хранилище ключей требуется только пароль сертификата. Key Vault не хранит этот пароль, поэтому при экспорте сертификата пароль будет пустым.

## <a name="exporting-certificate-using-cli"></a>Экспорт сертификата с помощью CLI
С помощью следующей команды можно скачать **открытую часть** сертификата Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Примеры и определения параметров см. в [этом разделе](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download).



Если вы хотите скачать сертификат полностью, т. е. **открытый и закрытый его компоненты**, его нужно скачать в качестве секрета.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
Определения параметров см. в [этом разделе](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download).


## <a name="exporting-certificate-using-powershell"></a>Экспорт сертификата с помощью PowerShell

Эта команда возвращает сертификат с именем TestCert01 из хранилища ключей с именем ContosoKV01. Чтобы скачать сертификат в виде PFX-файла, выполните следующую команду. Эти команды обращаются к параметру SecretId, а затем сохраняют его содержимое в виде PFX-файла.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
При этом будет экспортирована вся цепочка сертификатов с закрытым ключом, а сам сертификат будет защищен паролем.
Дополнительные сведения о команде ```Get-AzKeyVaultCertificate``` и параметрах см. в примере 2 [этой статьи](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0).

## <a name="exporting-certificate-using-portal"></a>Экспорт сертификата с помощью портала

При создании или импорте сертификата в колонке "Сертификат" на портале вы получите уведомление об успешном выполнении операции. Если выбрать сертификат и щелкнуть его текущую версию, отобразятся варианты его скачивания.


Чтобы скачать сертификат, нажмите кнопку "Скачать в формате CER" либо "Скачать в формате PFX или PEM".


![Скачивание сертификата](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Экспорт сертификатов службы приложений из хранилища ключей

Сертификаты службы приложений Azure обеспечивают удобный способ приобретения SSL-сертификатов и их назначения приложениям Azure непосредственно на портале. Эти сертификаты также можно экспортировать с портала в виде PFX-файлов.
Импортированные сертификаты службы приложений **находятся в разделе секретов**.

Инструкции по экспорту сертификатов службы приложений см. [здесь](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

## <a name="read-more"></a>Дополнительные сведения
* [Типы и расширения файлов SSL/TLS-сертификатов](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)