---
title: Создайте учетную запись хранилища BLOB-объектов блок - хранилища Azure | Документация Майкрософт
description: Показано, как создать учетную запись хранилища BLOB-объектов Azure block с характеристиками производительности уровня "премиум".
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141000"
---
# <a name="create-a-block-blob-storage-account"></a>Создание учетной записи хранения блочных BLOB-объектов

Тип учетной записи хранилища BLOB-объектов блока позволяет создавать блочных BLOB-объектов с характеристиками производительности уровня "премиум". Этот тип учетной записи хранения оптимизирован для рабочих нагрузок с учетом внешней скорости высоким числом транзакций или, для которых требуется очень быстрое время доступа. В этой статье показано, как создать учетную запись хранилища BLOB-объектов блока с помощью портала Azure, Azure CLI или Azure PowerShell.

Дополнительные сведения об учетных записях хранения BLOB-объектов блока см. в разделе [Обзор учетной записи хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Создание учетной записи на портале Azure

Чтобы создать учетную запись хранения BLOB-объектов блок на портале Azure, выполните следующие действия.

1. На портале Azure выберите **все службы** > **хранения** категории > **учетные записи хранения**.

1. В разделе **учетные записи хранения**выберите **добавить**.

1. В **подписки** выберите подписку, в которой для создания учетной записи хранения.

1. В **группы ресурсов** выберите существующую группу ресурсов или выберите **создать**и введите имя для новой группы ресурсов.

1. В **имя учетной записи хранения** введите имя для учетной записи. Обратите внимание на следующие рекомендации:

   - Имя должно быть уникальным в Azure.
   - Имя должно быть от 3 до 24 символов.
   - Имя может содержать только цифры и строчные буквы.

1. В **расположение** выберите расположение для учетной записи хранения или используйте расположение по умолчанию.

1. Для остальных параметров настройте следующие параметры:

   |Поле     |Value  |
   |---------|---------|
   |**Производительность**    |  Выберите **уровня "премиум"**.   |
   |**Account kind** (Тип учетной записи)    | Выберите **BlockBlobStorage**.      |
   |**Репликация**    |  Оставьте значение по умолчанию **локально избыточное хранилище (LRS)**.      |

   ![Показывает пользовательский Интерфейс для создания учетной записи хранения BLOB-объектов блок портала](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Выберите **просмотрите + создать** для просмотра параметров учетной записи хранения.

1. Нажмите кнопку **Создать**.

## <a name="create-account-using-azure-powershell"></a>Создание учетной записи, с помощью Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Откройте сеанс Windows PowerShell с повышенными правами (Запуск от имени администратора).

1. Выполните следующую команду, чтобы убедиться, что последняя версия `Az` установлен модуль PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Откройте новую консоль PowerShell и войдите с помощью учетной записи Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. При необходимости создайте новую группу ресурсов. Замените значения в кавычках и выполните следующую команду.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Создание учетной записи хранения большой двоичный объект блока. Замените значения в кавычках и выполните следующую команду.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Создание учетной записи, с помощью Azure CLI

Чтобы создать учетную запись большого двоичного объекта блока с помощью Azure CLI, необходимо сначала установить интерфейс командной строки Azure. 2.0.46 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Войдите в свою подписку Azure.

   ```azurecli
   az login
   ```

1. При необходимости создайте новую группу ресурсов. Замените значения в скобках (включая скобки) и выполните следующую команду.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Создание учетной записи хранения большой двоичный объект блока. Замените значения в скобках (включая скобки) и выполните следующую команду.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об учетных записях хранения см. в статье [Общие сведения об учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
