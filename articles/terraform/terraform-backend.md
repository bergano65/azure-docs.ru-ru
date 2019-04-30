---
title: Использование службы хранилища Azure в качестве сервера Terraform
description: Общие сведения о сохранении состояния Terraform в службе хранилища Azure.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: 7145a50bc53fd28afafd3de9c724b5e5f71624fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905890"
---
# <a name="store-terraform-state-in-azure-storage"></a>Хранение состояния Terraform в службе хранилища Azure

Состояние Terraform используется для согласования развернутых ресурсов с конфигурациями Terraform. Благодаря состоянию Terraform известно, какие ресурсы Azure следует добавить, изменить или удалить. По умолчанию состояние Terraform хранится локально при выполнении *применения Terraform*. Такая конфигурация не идеальна по ряду причин.

- Локальное состояние плохо подходит для групповой среды или среды совместной работы.
- Состояние Terraform может включать в себя конфиденциальные сведения.
- Локальное хранение состояния повышает риск непреднамеренного удаления.

В Terraform есть понятие сервера состояния, то есть удаленного хранилища для состояния Terraform. При использовании хранилища состояния файл состояния хранится в источнике данных, например в службе хранилища Azure. В этом документе описывается, как настроить и использовать службу хранилища Azure в качестве сервера состояния Terraform.

## <a name="configure-storage-account"></a>Настройка учетной записи хранения

Прежде чем использовать службу хранилища Azure в качестве сервера, необходимо создать учетную запись хранения. Это можно сделать на портале Azure, с помощью PowerShell, Azure CLI или самого средства Terraform. Чтобы настроить учетную запись хранения с помощью Azure CLI, воспользуйтесь приведенным ниже примером.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Запишите имя учетной записи хранения, имя контейнера и ключ доступа к хранилищу. Эти значения требуются при настройке удаленного состояния.

## <a name="configure-state-backend"></a>Настройка сервера состояния

Сервер состояния Terraform настраивается при выполнении *инициализации Terraform*. Чтобы настроить сервер состояния, требуются следующие данные:

- storage_account_name — имя учетной записи хранения Azure;
- container_name — имя контейнера больших двоичных объектов;
- key — имя файла хранилища состояния, который необходимо создать;
- access_key — ключ доступа к хранилищу.

Каждое из этих значений можно указать в файле конфигурации Terraform или в командной строке, однако для `access_key` рекомендуется использовать переменную среды. Это предотвращает запись ключа на диск.

Создайте переменную среды `ARM_ACCESS_KEY`, значением которой является ключ доступа к хранилищу Azure.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Чтобы еще надежнее защитить ключ доступа к учетной записи хранения Azure, храните его в Azure Key Vault. В этом случае переменную среды можно задать с помощью команды, наподобие приведенной ниже. Дополнительные сведения об Azure Key Vault см. в [документации по Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Чтобы настроить Terraform для использования сервера, включите блок конфигурации *backend* типа *azurerm* в конфигурацию Terraform. Добавьте значения *storage_account_name*, *container_name* и *key* в блок конфигурации.

В приведенном ниже примере настраивается сервер Terraform и создается группа ресурсов Azure. Замените значения значениями для своей среды.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Теперь инициализируйте конфигурацию с помощью команды *Terraform init*, а затем примените ее с помощью команды *Terraform apply*. По завершении файл состояния будет находиться в Azure Storage Blob.

## <a name="state-locking"></a>Блокировка состояния

При использовании Azure Storage Blob для хранения состояния большой двоичный объект автоматически блокируется до выполнения любых операций записи состояния. Благодаря такой конфигурации предотвращается параллельное выполнение нескольких операций с состоянием, которое может привести к повреждению. Дополнительные сведения см. в разделе [State Locking][terraform-state-lock] (Блокировка состояния) в документации Terraform.

Блокировку можно увидеть при просмотре большого двоичного объекта на портале Azure или с помощью других средств управления Azure.

![BLOB-объект Azure с блокировкой](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Шифрование при хранении

По умолчанию данные в большом двоичном объекте Azure шифруются перед сохранением в инфраструктуре хранения. Когда средству Terraform требуется состояние, оно извлекается с сервера и сохраняется в памяти системы разработки. Благодаря такой конфигурации состояние безопасно хранится в службе хранилища Azure и не записывается на локальный диск.

Дополнительные сведения о шифровании в службе хранилища Azure см. в статье [Шифрование службы хранилища Azure для неактивных данных][azure-storage-encryption].

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о конфигурации сервера Terraform см. в [документации по серверу Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
