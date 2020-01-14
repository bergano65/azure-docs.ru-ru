---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: e479f2376668a2fc3824e733996c94cfab04c9ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466959"
---
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью команды [az group create](/cli/azure/group). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Создайте учетную запись хранения общего назначения с помощью команды [az storage account create](/cli/azure/storage/account). Эта учетная запись хранения может использоваться для всех четырех служб: больших двоичных объектов, файлов, таблиц и очередей.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Указание учетных данных учетной записи хранения

Azure CLI требуются учетные данные учетной записи хранения для большинства команд в этом руководстве. Хотя для этого есть несколько вариантов, проще всего предоставить их, задав переменные среды `AZURE_STORAGE_ACCOUNT` и `AZURE_STORAGE_KEY`.

> [!NOTE]
> В этой статье показано, как задать переменные среды с помощью Bash. В других средах может потребоваться внести изменения в синтаксис.

Сначала отобразите ключи своей учетной записи хранения с помощью команды [az storage account keys list](/cli/azure/storage/account/keys). Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Теперь задайте переменные среды `AZURE_STORAGE_ACCOUNT` и `AZURE_STORAGE_KEY`. Это можно сделать в оболочке Bash с помощью команды `export`. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Дополнительные сведения о том, как получить ключи доступа к учетной записи с помощью портала Azure, см. в разделе [Управление ключами доступа к учетной записи хранения](../articles/storage/common/storage-account-keys-manage.md).