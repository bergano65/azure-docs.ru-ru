---
title: Создание учетной записи хранилища блочных BLOB-объектов в службе хранилища Azure | Документация Майкрософт
description: Здесь показано, как создать учетную запись Azure Блоккблобстораже с характеристиками производительности "Премиум".
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79536910"
---
# <a name="create-a-blockblobstorage-account"></a>Создание учетной записи Блоккблобстораже

Тип учетной записи Блоккблобстораже позволяет создавать блочные BLOB-объекты с характеристиками производительности Premium. Этот тип учетной записи хранения оптимизирован для рабочих нагрузок с высокими тарифами на транзакции или для которых требуется очень быстрое время доступа. В этой статье показано, как создать учетную запись Блоккблобстораже с помощью портал Azure, Azure CLI или Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Дополнительные сведения об учетных записях Блоккблобстораже см. в статье [Обзор учетной записи хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

# <a name="portal"></a>[Портал](#tab/azure-portal)

Отсутствует.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для работы с этой статьей требуется модуль Azure PowerShell AZ Version 1.2.0 или более поздней версии. Чтобы узнать, какая версия используется сейчас, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов:

- Команды интерфейса командной строки можно выполнять в портал Azure в Azure Cloud Shell.
- Вы можете установить интерфейс командной строки и выполнить команды CLI локально.

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Azure CLI предварительно установлен и настроен для использования с вашей учетной записью. Нажмите кнопку **Cloud Shell** в меню в правой верхней части портал Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Кнопка запускает интерактивную оболочку, которую можно использовать для выполнения действий, описанных в этой статье.

[![Снимок экрана, показывающий окно Cloud Shell на портале](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Установка CLI локально

Azure CLI также можно установить и применять локально. В этом руководстве требуется, чтобы вы выполняли Azure CLI версии 2.0.46 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Вход в Azure

# <a name="portal"></a>[Портал](#tab/azure-portal)

Войдите на [портал Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнить проверку подлинности, войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы запустить Azure Cloud Shell, войдите в [портал Azure](https://portal.azure.com).

Чтобы войти в локальную установку интерфейса командной строки, выполните команду [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Создание учетной записи Блоккблобстораже

## <a name="portal"></a>[Портал](#tab/azure-portal)
Чтобы создать учетную запись Блоккблобстораже в портал Azure, выполните следующие действия.

1. В портал Azure выберите **все службы** > категорию **хранилища** > **учетные записи хранения**.

1. В разделе **учетные записи хранения**выберите **Добавить**.

1. В поле **Подписка** выберите подписку, в которой будет создана учетная запись хранения.

1. В поле **Группа ресурсов** выберите существующую группу ресурсов или щелкните **создать**и введите имя новой группы ресурсов.

1. В поле **имя учетной записи хранения** введите имя учетной записи. Обратите внимание на следующие рекомендации.

   - Это имя должно быть уникальным в пределах Azure.
   - Длина имени должна составлять от 3 до 24 символов.
   - Имя может содержать только цифры и строчные буквы.

1. В поле **Расположение** выберите расположение для учетной записи хранения или используйте расположение по умолчанию.

1. Для остальных параметров настройте следующие параметры.

   |Поле     |Значение  |
   |---------|---------|
   |**Производительность**    |  Выберите **Premium**.   |
   |**Тип учетной записи**    | Выберите **блоккблобстораже**.      |
   |**Репликация**    |  Оставьте значение по умолчанию для **локально избыточного хранилища (LRS)**.      |

   ![Отображение пользовательского интерфейса портала для создания учетной записи хранения блочного BLOB-объекта](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Выберите **проверить и создать** , чтобы проверить параметры учетной записи хранения.

1. Щелкните **Создать**.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Откройте сеанс Windows PowerShell с повышенными привилегиями (Запуск от имени администратора).

1. Выполните следующую команду, чтобы убедиться, что установлена последняя версия модуля `Az` PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Откройте новую консоль PowerShell и выполните вход с помощью учетной записи Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. При необходимости создайте новую группу ресурсов. Замените значения в кавычках и выполните следующую команду.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Создайте учетную запись Блоккблобстораже. Замените значения в кавычках и выполните следующую команду.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы создать учетную запись блочного BLOB-объекта с помощью Azure CLI, необходимо сначала установить Azure CLI v. 2.0.46 или более поздняя версия. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Войдите в свою подписку Azure.

   ```azurecli
   az login
   ```

1. При необходимости создайте новую группу ресурсов. Замените значения в квадратных скобках (включая квадратные скобки) и выполните следующую команду.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Создайте учетную запись Блоккблобстораже. Замените значения в квадратных скобках (включая квадратные скобки) и выполните следующую команду.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения об учетных записях хранения см. в статье [Общие сведения об учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
