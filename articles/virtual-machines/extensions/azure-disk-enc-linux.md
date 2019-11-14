---
title: Шифрование дисков Azure для Linux
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
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073811"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Шифрование дисков Azure для Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Обзор

Шифрование дисков Azure использует подсистему dm-crypt в Linux для шифрования всего диска в [выбранных дистрибутивах Azure Linux](https://aka.ms/adelinux).  Решение интегрируется с Azure Key Vault, обеспечивая управление секретами и ключами шифрования диска.

## <a name="prerequisites"></a>предварительным требованиям

Полный список необходимых компонентов см. в разделах [Шифрование дисков Azure для виртуальных машин Linux](../linux/disk-encryption-overview.md), в частности следующие разделы:

- [Шифрование дисков Azure для виртуальных машин Linux](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Дополнительные требования к виртуальным машинам](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Требования к сети](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Schemata расширения

Существует два Schemata для шифрования дисков Azure: v 1.1, более новая, Рекомендуемая схема, которая не использует свойства Azure Active Directory (AAD) и v 0,1, более старая схема, для которой требуются свойства AAD. Необходимо использовать версию схемы, соответствующую используемому расширению: schema v 1.1 для расширения AzureDiskEncryptionForLinux версии 1,1, схема v 0,1 для расширения AzureDiskEncryptionForLinux версии 0,1.
### <a name="schema-v11-no-aad-recommended"></a>Схема v 1.1: нет AAD (рекомендуется)

Рекомендуется использовать схему версии 1.1 и не требует Azure Active Directory свойств.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Схема v 0,1: с AAD 

Схема 0,1 требует `aadClientID` и либо `aadClientSecret`, либо `AADClientCertificate`.

С помощью `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

С помощью `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Значения свойств

| имя | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | дата |
| publisher | Microsoft.Azure.Security | строка |
| type | AzureDiskEncryptionForLinux | строка |
| typeHandlerVersion | 0.1, 1.1 | int |
| (схема 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (схема 0,1) AADClientSecret | password | строка |
| (схема 0,1) аадклиентцертификате | thumbprint | строка |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Словарь JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | строка | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | строка |
| KeyEncryptionKeyURL | url | строка |
| используемых KeyVaultURL | url | строка |
| Парольная фраза | password | строка | 
| SequenceVersion | uniqueidentifier | строка |
| VolumeType | ОС, данные, все | строка |

## <a name="template-deployment"></a>Развертывание шаблона

Пример шаблона развертывания, см. в руководстве по [включению шифрования на работающей виртуальной машине Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Инструкции см. в последней версии [документации по Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Дополнительные сведения см. в [руководстве по устранению неполадок с шифрованием дисков Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/community/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о расширениях виртуальных машин см. в обзоре [расширений и компонентов виртуальной машины для Linux](features-linux.md).