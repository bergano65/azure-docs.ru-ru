---
title: Шифрование дисков Azure для Windows | Документация Майкрософт
description: Развертывание шифрования дисков Azure на виртуальной машине Windows с помощью расширения виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 46699fb1add42d23a11234d5cd05e4a9627a91fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800042"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Шифрование дисков Azure для Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Обзор

При шифровании дисков Azure используется Bitlocker для полного шифрования диска на виртуальных машинах Azure под управлением Windows.  Шифрование дисков Azure интегрировано в Azure Key Vault, что позволяет управлять секретами и ключами шифрования дисков в подписке Key Vault. 

## <a name="prerequisites"></a>Технические условия

См. полный список [необходимых компонентов для шифрования дисков Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Операционная система

Список текущих поддерживаемых версий Windows см. в разделе с описанием [необходимых компонентов для шифрования дисков Azure](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Подключение к Интернету

При шифровании дисков Azure требуется подключение к Интернету для доступа к Active Directory, хранилищу Key Vault, хранения и конечным точкам управления пакетами.  Список параметров безопасности сети см. в разделе с описанием [необходимых компонентов для шифрования дисков Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

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

      "EncryptionOperation": "[encryptionOperation]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| тип | AzureDiskEncryptionForWindows| string |
| typeHandlerVersion | 1.0, 1.1, 2.2 (VMSS) | int |
| (Необязательно) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (Необязательно) AADClientSecret | password | string |
| (Необязательно) AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption | string | 
| KeyEncryptionAlgorithm | RSA-OAEP, RSA1_5 | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultResourceId | uri ресурса | string |
| KekVaultResourceId | uri ресурса | string |
| KeyVaultURL | url | string |
| SequenceVersion | uniqueidentifier | string |
| VolumeType | ОС, данные, все | string |

## <a name="template-deployment"></a>Развертывание шаблона
Пример шаблона развертывания см. в руководстве по [созданию зашифрованной виртуальной машины Windows из образа коллекции](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Инструкции см. в последней версии [документации по Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Дополнительные сведения см. в [руководстве по устранению неполадок с шифрованием дисков Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/community/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дальнейшие действия
См. дополнительные сведения о [расширениях и компонентах виртуальных машин Windows](features-windows.md).
