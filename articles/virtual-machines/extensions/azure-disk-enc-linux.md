---
title: Лазурное шифрование дисков для Linux
description: Развертывание шифрования дисков Azure на виртуальной машине Linux с помощью расширения виртуальной машины.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066920"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Шифрование дисков Azure для Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Обзор

Шифрование дисков Azure использует подсистему dm-crypt в Linux для шифрования всего диска в [выбранных дистрибутивах Azure Linux](https://aka.ms/adelinux).  Решение интегрируется с Azure Key Vault, обеспечивая управление секретами и ключами шифрования диска.

## <a name="prerequisites"></a>Предварительные требования

Полный список предпосылок можно узнать в разделе [«Шифрование дисков Azure» для Linux VMs,](../linux/disk-encryption-overview.md)в частности следующие разделы:

- [Поддерживаемые ВМ и операционные системы](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Дополнительные требования к VM](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Требования к сети](../linux/disk-encryption-overview.md#networking-requirements)
- [Требования к хранению ключей шифрования](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Схема расширения

Существует две версии схемы расширения для шифрования azure Disk (ADE):
- v1.1 - Новая рекомендуемая схема, которая не использует свойства Azure Active Directory (AAD).
- v0.1 - Старая схема, требующая свойств активного каталога Azure (AAD). 

Для выбора целевой схемы `typeHandlerVersion` свойство должно быть равно варианту схемы, которая вы хотите использовать.

### <a name="schema-v11-no-aad-recommended"></a>Схема v1.1: Нет AAD (рекомендуется)

Схема v1.1 рекомендуется и не требует свойств Active Directory (AAD) Azure.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Схема v0.1: с AAD 

Схема 0.1 требует `AADClientID` и `AADClientSecret` либо `AADClientCertificate`или .

Использование среды `AADClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Использование среды `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Значения свойств

| name | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2019-07-01 | Дата |
| publisher | Microsoft.Azure.Security | строка |
| type | AzureDiskEncryptionForLinux | строка |
| typeHandlerVersion | 1.1, 0.1 | INT |
| (0,1 схема) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1 схема) AADClientSecret | password | строка |
| (0,1 схема) AADClientCertificate | thumbprint | строка |
| (необязательно) (0,1 схема) Парольную фразу | password | строка |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Словарь JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | строка | 
| (необязательно - по умолчанию RSA-OAEP) KeyEncryptionАлгоритм | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | строка |
| KeyVaultURL | url | строка |
| KeyVaultResourceId | url | строка |
| (необязательно) KeyEncryptionKeyURL | url | строка |
| (необязательно) KekVaultResourceId | url | строка |
| (необязательно) ПроквиачинаВерсия | UNIQUEIDENTIFIER | строка |
| VolumeType | ОС, данные, все | строка |

## <a name="template-deployment"></a>Развертывание шаблона

На примере развертывания шаблонов, основанного на схеме v1.1, см. [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)

На примере развертывания шаблонов на основе схемы v0.1 см. [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)

>[!WARNING]
> - Если ранее для шифрования VM использовалось шифрование Azure Disk с Azure AD, необходимо продолжить использование этой опции для шифрования VM.
> - При шифровании объемов Linux OS VM следует считать недоступным. Мы настоятельно рекомендуем избегать логинов SSH во время шифрования, чтобы избежать проблем, блокирующих любые открытые файлы, которые должны быть доступны во время процесса шифрования. Чтобы проверить прогресс, используйте [Get-AzVMDiskEncryptionStatus PowerShell](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet или [vm шифрование показать](/cli/azure/vm/encryption#az-vm-encryption-show) команду CLI. Этот процесс может занять несколько часов для тома операционной системы 30 ГБ, а также дополнительное время для шифрования томов данных. Если не используется параметр шифрования всех форматов, время шифрования тома данных будет соответствовать размеру и количеству томов данных. 
> - Отключение шифрования на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС. 

>[!NOTE]
> Кроме `VolumeType` того, если параметр установлен для всех, диски данных будут зашифрованы только в том случае, если они правильно установлены.

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Дополнительные сведения см. в [руководстве по устранению неполадок с шифрованием дисков Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, вы можете связаться с экспертами Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/community/) 

Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите в [службу поддержки Azure](https://azure.microsoft.com/support/options/) и выберите Поддержку Получить. Для получения информации об использовании службы поддержки Azure прочитайте [часто задаваемые вопросы поддержки Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о расширениях виртуальных машин см. в обзоре [расширений и компонентов виртуальной машины для Linux](features-linux.md).
* Для получения дополнительной информации о шифровании дисков Azure для Linux [см.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)
