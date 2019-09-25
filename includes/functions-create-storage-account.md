---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203173"
---
## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

В службе "Функции" используется учетная запись хранения Azure общего назначения для сохранения состояния и других сведений о функциях. Создайте учетную запись хранения общего назначения в созданной вами группе ресурсов с помощью команды [az storage account create](/cli/azure/storage/account#az-storage-account-create).

В следующей команде замените `<storage_name>` глобально уникальным именем своей учетной записи хранения везде, где встречается этот заполнитель. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
