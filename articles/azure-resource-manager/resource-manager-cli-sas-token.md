---
title: Развертывание шаблона Azure с помощью токена SAS и Azure CLI | Документы Майкрософт
description: Используйте Azure Resource Manager и Azure CLI для развертывания ресурсов в Azure из шаблона, защищенного с помощью токена SAS.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: a71698831859e92300706ade8c2284cbfc7ee241
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205406"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure CLI

Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS). В этом разделе объясняется, как использовать Azure PowerShell с шаблонами Resource Manager для предоставления токена SAS во время развертывания. 

## <a name="add-private-template-to-storage-account"></a>Добавление частного шаблона в учетную запись хранения

Шаблоны можно добавить в учетную запись хранения и создать ссылки на них во время развертывания с помощью маркера SAS.

> [!IMPORTANT]
> Если выполнить приведенные ниже действия, то большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Применение маркера SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.
> 
> 

Приведенный ниже пример позволяет настроить контейнер учетной записи хранения и передать шаблон:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Предоставление маркера SAS во время развертывания
Чтобы развернуть частный шаблон в учетной записи хранения, создайте маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона. Задайте срок действия, достаточный для выполнения развертывания.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Пример использования маркера SAS со связанными шаблонами см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Дальнейшие действия
* Вводные сведения о развертывании шаблонов см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy-cli.md).
* Полный пример сценария, развертывающего шаблон, см. в статье [Сценарий для развертывания шаблона Resource Manager](resource-manager-samples-cli-deploy.md).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
