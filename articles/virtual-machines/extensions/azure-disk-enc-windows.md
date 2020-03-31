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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066865"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Шифрование дисков Azure для Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Обзор

При шифровании дисков Azure используется Bitlocker для полного шифрования диска на виртуальных машинах Azure под управлением Windows.  Шифрование дисков Azure интегрировано в Azure Key Vault, что позволяет управлять секретами и ключами шифрования дисков в подписке Key Vault. 

## <a name="prerequisites"></a>Предварительные требования

Полный список предпосылок можно узнать [в разделе «Шифрование дисков Azure» для Windows VMs,](../windows/disk-encryption-overview.md)в частности следующие разделы:

- [Поддерживаемые ВМ и операционные системы](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Требования к сети](../windows/disk-encryption-overview.md#networking-requirements)
- [Требования к групповой политике](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Схема расширения

Существует две версии схемы расширения для шифрования azure Disk (ADE):
- v2.2 - Новая рекомендуемая схема, которая не использует свойства Azure Active Directory (AAD).
- v1.1 - Старая схема, требующая свойств активного каталога Azure (AAD). 

Для выбора целевой схемы `typeHandlerVersion` свойство должно быть равно варианту схемы, которая вы хотите использовать.

### <a name="schema-v22-no-aad-recommended"></a>Схема v2.2: Нет AAD (рекомендуется)

Схема v2.2 рекомендуется для всех новых VM и не требует свойств Active Directory Azure.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Схема v1.1: с AAD 

Схема 1.1 требует `aadClientID` и `aadClientSecret` либо `AADClientCertificate` или не рекомендуется для новых VM.

Использование среды `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
| type | ЛазурдискШифрование | строка |
| typeHandlerVersion | 2.2, 1.1 | строка |
| (1.1 схема) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1 схема) AADClientSecret | password | строка |
| (1.1 схема) AADClientCertificate | thumbprint | строка |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | строка | 
| (необязательно - по умолчанию RSA-OAEP) KeyEncryptionАлгоритм | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | строка |
| KeyVaultURL | url | строка |
| KeyVaultResourceId | url | строка |
| (необязательно) KeyEncryptionKeyURL | url | строка |
| (необязательно) KekVaultResourceId | url | строка |
| (необязательно) ПроквиачинаВерсия | UNIQUEIDENTIFIER | строка |
| VolumeType | ОС, данные, все | строка |

## <a name="template-deployment"></a>Развертывание шаблона

На примере развертывания шаблонов, основанного на схеме v2.2, см. [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)

На примере развертывания шаблонов, основанного на схеме v1.1, см. [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

>[!NOTE]
> Кроме `VolumeType` того, если параметр установлен для всех, диски данных будут зашифрованы только в том случае, если они должным образом отформатированы. 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Дополнительные сведения см. в [руководстве по устранению неполадок с шифрованием дисков Azure](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, вы можете связаться с экспертами Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/community/) 

Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите в [службу поддержки Azure](https://azure.microsoft.com/support/options/) и выберите Поддержку Получить. Для получения информации об использовании службы поддержки Azure прочитайте [часто задаваемые вопросы поддержки Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о [расширениях и компонентах виртуальных машин Windows](features-windows.md).
* Для получения дополнительной информации о шифровании дисков Azure для Windows [см.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)
