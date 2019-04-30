---
title: Шифрование дисков Azure для Linux | Документация Майкрософт
description: Развертывание шифрования дисков Azure на виртуальной машине Linux с помощью расширения виртуальной машины.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 3ce881da4b683cf7034100d5044dd0f3c93edb52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800184"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Шифрование дисков Azure для Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Обзор

Шифрование дисков Azure использует подсистему dm-crypt в Linux для шифрования всего диска в [выбранных дистрибутивах Azure Linux](https://aka.ms/adelinux).  Решение интегрируется с Azure Key Vault, обеспечивая управление секретами и ключами шифрования диска.

## <a name="prerequisites"></a>Технические условия

См. полный список [необходимых компонентов для шифрования дисков Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Операционная система

Шифрование дисков Azure сейчас поддерживается для некоторых дистрибутивов и версий.  Список поддерживаемых дистрибутивов Linux см. в разделе с [вопросами и ответами о шифровании дисков Azure](../../security/azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).

### <a name="internet-connectivity"></a>Подключение к Интернету

При шифровании дисков Azure для Linux требуется подключение к Интернету для доступа к Active Directory, хранилищу Key Vault, хранения и конечным точкам управления пакетами.  См. список [необходимых компонентов для шифрования дисков Azure](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Схема расширения

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

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| тип | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1.1 (VMSS) | int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| AADClientSecret | password | string |
| AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Словарь JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| Парольная фраза | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | ОС, данные, все | string |

## <a name="template-deployment"></a>Развертывание шаблона

Пример шаблона развертывания, см. в руководстве по [включению шифрования на работающей виртуальной машине Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Инструкции см. в последней версии [документации по Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Дополнительные сведения см. в [руководстве по устранению неполадок с шифрованием дисков Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/community/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о расширениях виртуальных машин см. в обзоре [расширений и компонентов виртуальной машины для Linux](features-linux.md).