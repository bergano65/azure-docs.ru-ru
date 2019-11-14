---
title: Шифрование дисков Azure для Windows
description: Развертывание шифрования дисков Azure на виртуальной машине Windows с помощью расширения виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073211"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Шифрование дисков Azure для Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Обзор

При шифровании дисков Azure используется Bitlocker для полного шифрования диска на виртуальных машинах Azure под управлением Windows.  Шифрование дисков Azure интегрировано в Azure Key Vault, что позволяет управлять секретами и ключами шифрования дисков в подписке Key Vault. 

## <a name="prerequisites"></a>предварительным требованиям

Полный список необходимых компонентов см. в разделах [Шифрование дисков Azure для виртуальных машин Linux](../linux/disk-encryption-overview.md), в частности следующие разделы:

- [Шифрование дисков Azure для виртуальных машин Linux](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Требования к сети](../windows/disk-encryption-overview.md#networking-requirements)
- [Требования к групповая политика](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Schemata расширения

Существует два Schemata для расширения Windows AzureDiskEncryption: v 2.2, более новая, Рекомендуемая схема, которая не использует свойства Azure Active Directory (AAD) и v 1.1, более старая схема, для которой требуются свойства AAD. Необходимо использовать версию схемы, соответствующую используемому расширению: schema v 2.2 для расширения AzureDiskEncryption версии 2,2, Schema v 1.1 для расширения AzureDiskEncryption версии 1,1.

### <a name="schema-v22-no-aad-recommended"></a>Схема v 2.2: нет AAD (рекомендуется)

Схема v 2.2 рекомендуется для всех новых виртуальных машин и не требует Azure Active Directory свойств.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>Схема v 1.1: с AAD 

Схема 1,1 требует `aadClientID` и либо `aadClientSecret`, либо `AADClientCertificate` и не рекомендуется для новых виртуальных машин.

С помощью `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
| typeHandlerVersion | 1,1, 2,2 | строка |
| (схема 1,1) аадклиентид | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (схема 1,1) AADClientSecret | password | строка |
| (схема 1,1) аадклиентцертификате | thumbprint | строка |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Словарь JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | строка | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | строка |
| KeyEncryptionKeyURL | url | строка |
| KeyVaultURL | url | строка |
| (необязательно) Passphrase | password | строка | 
| SequenceVersion | uniqueidentifier | строка |
| VolumeType | ОС, данные, все | строка |

## <a name="template-deployment"></a>Развертывание шаблона
Пример шаблона развертывания см. в руководстве по [созданию зашифрованной виртуальной машины Windows из образа коллекции](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Инструкции см. в последней версии [документации по Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Дополнительные сведения см. в [руководстве по устранению неполадок с шифрованием дисков Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/community/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дополнительная информация
См. дополнительные сведения о [расширениях и компонентах виртуальных машин Windows](features-windows.md).
