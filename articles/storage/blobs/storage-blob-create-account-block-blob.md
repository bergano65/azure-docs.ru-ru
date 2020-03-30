---
title: Создайте учетную запись хранения блоков blob - Azure Storage Документы Майкрософт
description: Показывает, как создать учетную запись Azure BlockBlobStorage с высокими характеристиками производительности.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536910"
---
# <a name="create-a-blockblobstorage-account"></a>Создание учетной записи BlockBlobStorage

Вид учетной записи BlockBlobStorage позволяет создавать блок капли с премиальными характеристиками производительности. Этот тип учетной записи хранения оптимизирован для рабочих нагрузок с высокими ставками транзакций или требует очень быстрого времени доступа. В этой статье показано, как создать учетную запись BlockBlobStorage с помощью портала Azure, Azure CLI или Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Для получения дополнительной информации об [Azure storage account overview](https://docs.microsoft.com/azure/storage/common/storage-account-overview)учетных записях BlockBlobStorage см.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

# <a name="portal"></a>[Портал](#tab/azure-portal)

Нет.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для этой статьи требуется модуль Azure PowerShell Az версии 1.2.0 или позже. Чтобы узнать, какая версия используется сейчас, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов:

- Вы можете запускать команды CLI с портала Azure в облачной оболочке Azure.
- Вы можете установить CLI и запустить команды CLI локально.

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Azure CLI предварительно установлен и настроен для использования в вашей учетной записи. Нажмите кнопку **Cloud Shell** в меню в правом верхнем разделе портала Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Кнопка запускает интерактивную оболочку, которую можно использовать для выполнения шагов, изложенных в этой статье:

[![Скриншот, показывающий окно облачной оболочки на портале](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Установка CLI локально

Azure CLI также можно установить и применять локально. Эта статья требует, чтобы вы запускали версию Azure CLI 2.0.46 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Вход в Azure

# <a name="portal"></a>[Портал](#tab/azure-portal)

Войдите на [портал Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнить проверку подлинности, войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы запустить облачную оболочку Azure, вопием на [порталaz.](https://portal.azure.com)

Чтобы войти в локальную установку CLI, запустите команду [входа в az:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Создание учетной записи BlockBlobStorage

## <a name="portal"></a>[Портал](#tab/azure-portal)
Чтобы создать учетную запись BlockBlobStorage на портале Azure, выполните следующие действия:

1. На портале Azure выберите **все службы** > категории **хранения** > **учетных записей.**

1. Под **счетом хранения**выберите **Добавить**.

1. В поле **подписки** выберите подписку, в которой можно создать учетную запись хранилища.

1. В поле **группы ресурсов** выберите существующую группу ресурсов или **создайте новую**и введите имя для новой группы ресурсов.

1. В поле **имени учетной записи Хранилища** введите имя учетной записи. Обратите внимание на следующие рекомендации:

   - Это имя должно быть уникальным в пределах Azure.
   - Имя должно быть от трех до 24 символов в длину.
   - Название может включать только номера и буквы нижнего регистра.

1. В поле **местоположения** выберите место для учетной записи хранилища или используйте местоположение по умолчанию.

1. Для остальных настроек настроите следующее:

   |Поле     |Значение  |
   |---------|---------|
   |**Производительность**    |  Выберите **Премиум**.   |
   |**Тип учетной записи**    | Выберите **BlockBlobStorage**.      |
   |**Репликации**    |  Оставьте настройку локально избыточного **хранилища (LRS)** по умолчанию.      |

   ![Отображает uI портала для создания учетной записи хранения блоков](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Выберите **Обзор и создайте** для просмотра параметров учетной записи хранилища.

1. Выберите **Создать**.

## <a name="azure-powershell"></a>[Лазурная powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Откройте повышенную сессию Windows PowerShell (запуск в качестве администратора).

1. Выполнить следующую команду, чтобы убедиться, что последняя версия модуля `Az` PowerShell установлена.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Откройте новую консоль PowerShell и вопийте с учетной записью Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. При необходимости создайте новую группу ресурсов. Замените значения в котировках и запустите следующую команду.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Создайте учетную запись BlockBlobStorage. Замените значения в котировках и запустите следующую команду.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы создать учетную запись block blob с помощью Azure CLI, необходимо сначала установить Azure CLI v. 2.0.46 или более поздняя версия. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Войдите в свою подписку Azure.

   ```azurecli
   az login
   ```

1. При необходимости создайте новую группу ресурсов. Замените значения в скобках (включая скобки) и запустите следующую команду.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Создайте учетную запись BlockBlobStorage. Замените значения в скобках (включая скобки) и запустите следующую команду.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об учетных записях хранения см. в статье [Общие сведения об учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
