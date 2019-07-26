---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444128"
---
## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

В службе "Функции" используется учетная запись хранения Azure общего назначения для сохранения состояния и других сведений о функциях. Создайте учетную запись хранения общего назначения в созданной вами группе ресурсов с помощью команды [az storage account create](/cli/azure/storage/account).

В следующей команде замените `<storage_name>` глобально уникальным именем своей учетной записи хранения везде, где встречается этот заполнитель. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
