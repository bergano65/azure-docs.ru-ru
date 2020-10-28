---
title: Элемент пользовательского интерфейса Кэйваултцертификатеселектор
description: Описывает элемент пользовательского интерфейса Microsoft. KeyVault. Кэйваултцертификатеселектор для портал Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 48aafceff80e4a570900e5a8e1190698e12946b1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754587"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Элемент пользовательского интерфейса Microsoft. KeyVault. Кэйваултцертификатеселектор

Элемент управления для выбора сертификата хранилища ключей.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

Пользователю предоставляется возможность выбрать доступный сертификат.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. KeyVault. Кэйваултцертификатеселектор":::

После выбора параметра **выбрать сертификат** пользователь может указать хранилище ключей и сертификат из хранилища ключей.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft. KeyVault. Кэйваултцертификатеселектор":::

Элемент управления обновляется для вывода выбранного хранилища ключей и имени сертификата.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. KeyVault. Кэйваултцертификатеселектор":::

## <a name="schema"></a>схема

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "Key Vault certificates selection"
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
