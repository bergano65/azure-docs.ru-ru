---
title: Настройка сертификата шифрования и шифрование секретов в кластерах Windows на платформе Azure Service Fabric | Документация Майкрософт
description: Узнайте, как настроить сертификат шифрования и шифровать секреты в кластерах Windows.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662318"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Настройка сертификата шифрования и шифрование секретов в кластерах Windows
В этой статье описывается, как настроить сертификат шифрования и использовать его для шифрования секретов в кластерах Windows. Инструкции для кластеров Linux приведены в разделе [Настройка сертификата шифрования и шифрование секретов в кластерах Linux][secret-management-linux-specific-link].

В качестве безопасного расположения для хранения сертификатов здесь используется [хранилище ключей Azure][key-vault-get-started]. С его помощью сертификаты также устанавливаются в кластеры Service Fabric в Azure. Если вы не выполняете развертывание в Azure, то использование хранилища ключей для управления секретами в приложениях Service Fabric не требуется. Однако *использование* секретов в приложении осуществляется без привязки к определенной облачной платформе, чтобы приложения могли быть развернуты в кластере, размещенном в любом месте. 

## <a name="obtain-a-data-encipherment-certificate"></a>Получение сертификата шифрования данных
Сертификат шифрования данных используются исключительно для шифрования и расшифровки [параметров][parameters-link] в файле Settings.xml службы и [переменных среды][environment-variables-link] в ее файле ServiceManifest.xml. Он не применяется для аутентификации или подписывания зашифрованного текста. Сертификат должен отвечать приведенным ниже требованиям.

* Сертификат должен содержать закрытый ключ.
* Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
* Использование ключа сертификата должно включать шифрование данных (10) и не должно включать аутентификацию сервера или клиента. 
  
  Например, при создании самозаверяющего сертификата с помощью PowerShell для флага `KeyUsage` необходимо задать значение `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Установка сертификата в кластере
Этот сертификат должен быть установлен на каждом узле в кластере. Инструкции по созданию и настройке кластера с помощью Azure Resource Manager см. в [этой статье][service-fabric-cluster-creation-via-arm]. 

## <a name="encrypt-application-secrets"></a>Шифрование секретов приложений
Для шифрования секрета используется следующая команда PowerShell. Эта команда шифрует только значение; она **не** подписывает зашифрованный текст. Чтобы создать зашифрованные данные для значений секретов, необходимо использовать тот же сертификат шифрования, установленный в кластере.

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Результирующая строка в кодировке Base-64 содержит как зашифрованные данные секрета, так и сведения о сертификате, с помощью которого выполнено шифрование секрета.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте, как [указать зашифрованные секреты в приложении][secret-management-specify-encrypted-secrets-link].

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
