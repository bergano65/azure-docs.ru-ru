---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d669874040b04af089b4d57333869fe34066720e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841837"
---
Самый простой способ проверить подлинность облачного приложения — с помощью управляемых удостоверений. Дополнительные сведения см. в статье [Provide Key Vault authentication with a managed identity](/azure/key-vault/general/managed-identity) (Предоставление проверки подлинности Key Vault с помощью управляемого удостоверения). 

Однако для простоты в рамках этого краткого руководства создается классическое приложение, требующее использования субъекта-службы и политики управления доступом. Для субъекта-службы требуется уникальное имя в формате http://&lt;уникальное_имя_субъекта-службы&gt;.

Создайте субъект-службу с помощью команды Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name>" --sdk-auth
```

Эта операция возвращает ряд пар "ключ — значение".

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
