---
title: Безопасноразразвертывание шаблон с токеном SAS
description: Развертывание ресурсов в Azure с шаблоном управления ресурсами Azure, защищенным токеном SAS. Отображает Azure PowerShell и Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156401"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Развертывание частного шаблона ARM с токеном SAS

Когда шаблон Azure Resource Manager (ARM) находится в учетной записи хранилища, можно ограничить доступ к шаблону, чтобы избежать публичного разоблачения. Вы получаете доступ к защищенному шаблону, создавая маркер общей подписи доступа (SAS) для шаблона и предоставляя этот маркер во время развертывания. В этой статье объясняется, как использовать Azure PowerShell или Azure CLI для развертывания шаблона с токеном SAS.

## <a name="create-storage-account-with-secured-container"></a>Создание учетной записи хранилища с защищенным контейнером

Следующий скрипт создает учетную запись хранения и контейнер с открытым доступом.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Загрузить шаблон в учетную запись хранения

Теперь вы готовы загрузить шаблон в учетную запись хранилища. Предоставьте путь к шаблону, который вы хотите использовать.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Предоставление маркера SAS во время развертывания

Чтобы развернуть частный шаблон в учетной записи хранения, создайте маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона. Задайте срок действия, достаточный для выполнения развертывания.

> [!IMPORTANT]
> Капля, содержащая шаблон, доступна только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Токен SAS является хорошим способом ограничения доступа к шаблонам, но вы не должны включать конфиденциальные данные, такие как пароли непосредственно в шаблоне.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Пример использования маркера SAS со связанными шаблонами см. в статье [Использование связанных шаблонов в Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Дальнейшие действия
* Для введения в развертывание шаблонов см. [Развертывание ресурсов с шаблонами ARM и Azure PowerShell.](deploy-powershell.md)
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](template-syntax.md#parameters).
