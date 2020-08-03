---
title: Создание учетной записи хранилища блочных BLOB-объектов в службе хранилища Azure | Документация Майкрософт
description: Здесь показано, как создать учетную запись Azure Блоккблобстораже с характеристиками производительности "Премиум".
author: tamram
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c23c034af40d7db2379f9d02357131a00482bd2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495749"
---
# <a name="create-a-blockblobstorage-account"></a>Создание учетной записи Блоккблобстораже

Тип учетной записи Блоккблобстораже позволяет создавать блочные BLOB-объекты с характеристиками производительности Premium. Этот тип учетной записи хранения оптимизирован для рабочих нагрузок с высокими тарифами на транзакции или для которых требуется очень быстрое время доступа. В этой статье показано, как создать учетную запись Блоккблобстораже с помощью портал Azure, Azure CLI или Azure PowerShell.

> [!NOTE]
> Функция иерархического пространства имен в учетной записи хранения блочных BLOB-объектов доступна в общедоступной предварительной версии и доступна в восточной части США, Восточная часть США 2, Центральная часть США, Юго-Центральный регион США, Западная часть США 2, южная часть Соединенного Королевства, Центральная Канада и Восточная Австралия регионах. Сведения об ограничениях см. [в разделе функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-blob-storage-features.md) и [известных проблемах](data-lake-storage-known-issues.md). Чтобы зарегистрироваться в предварительной версии, см. [эту форму](https://aka.ms/adlspremiumonboard).

Дополнительные сведения об учетных записях Блоккблобстораже см. в статье [Обзор учетной записи хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

# <a name="portal"></a>[Портал](#tab/azure-portal)

Отсутствует.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для работы с этой статьей требуется модуль Azure PowerShell AZ Version 1.2.0 или более поздней версии. Чтобы узнать, какая версия используется сейчас, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов:

- Выполнить команды CLI на портале Azure в Azure Cloud Shell.
- Установить CLI и выполнить команды CLI локально.

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Предварительно установленный интерфейс Azure CLI настроен для использования с вашей учетной записью. Нажмите кнопку **Cloud Shell** в меню в правой верхней части портал Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Кнопка запускает интерактивную оболочку, которую можно использовать для выполнения действий, описанных в этой статье.

[![Снимок экрана с окном Azure Cloud Shell на портале](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

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

2. В разделе **учетные записи хранения**выберите **Добавить**.

3. В поле **Подписка** выберите подписку, в которой будет создана учетная запись хранения.

4. В поле **Группа ресурсов** выберите существующую группу ресурсов или щелкните **создать**и введите имя новой группы ресурсов.

5. В поле **имя учетной записи хранения** введите имя учетной записи. Обратите внимание на следующие рекомендации.

   - Это имя должно быть уникальным в пределах Azure.
   - Длина имени должна составлять от 3 до 24 символов.
   - Имя может содержать только цифры и строчные буквы.

6. В поле **Расположение** выберите расположение для учетной записи хранения или используйте расположение по умолчанию.

7. Для остальных параметров настройте следующие параметры.

   |Поле     |Значение  |
   |---------|---------|
   |**Производительность**    |  Выберите **Premium**.   |
   |**Account kind** (Тип учетной записи)    | Выберите **блоккблобстораже**.      |
   |**Репликация**    |  Оставьте значение по умолчанию для **локально избыточного хранилища (LRS)**.      |

   ![Отображение пользовательского интерфейса портала для создания учетной записи хранения блочного BLOB-объекта](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. Перейдите на вкладку **Дополнительно** .

9. Если вы хотите оптимизировать учетную запись хранения для аналитики данных, установите для **иерархического пространства имен** значение **включено**. В противном случае оставьте для этого параметра значение по умолчанию.

   Дополнительные сведения см. в статье Общие сведения о [Azure Data Lake Storage 2-го поколения](data-lake-storage-introduction.md).

   > [!NOTE]
   > Функция иерархического пространства имен в учетной записи хранения блочных BLOB-объектов доступна в общедоступной предварительной версии и доступна в восточной части США, Восточная часть США 2, Центральная часть США, Юго-Центральный регион США, Западная часть США 2, южная часть Соединенного Королевства, Центральная Канада и Восточная Австралия регионах. Сведения об ограничениях см. [в разделе функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-blob-storage-features.md) и [известных проблемах](data-lake-storage-known-issues.md). Чтобы зарегистрироваться в предварительной версии, см. [эту форму](https://aka.ms/adlspremiumonboard).

8. Выберите **проверить и создать** , чтобы проверить параметры учетной записи хранения.

9. Щелкните **Создать**.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Откройте сеанс Windows PowerShell с повышенными привилегиями (Запуск от имени администратора).

2. Выполните следующую команду, чтобы убедиться, что установлена последняя версия `Az` модуля PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. Откройте новую консоль PowerShell и выполните вход с помощью учетной записи Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. При необходимости создайте новую группу ресурсов. Замените значения в кавычках и выполните следующую команду.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. Создайте учетную запись Блоккблобстораже. Замените значения в кавычках и выполните следующую команду.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   Если вы хотите оптимизировать учетную запись хранения для аналитики данных, добавьте ее `-EnableHierarchicalNamespace $True` в команду. Дополнительные сведения см. в статье Общие сведения о [Azure Data Lake Storage 2-го поколения](data-lake-storage-introduction.md).

   > [!NOTE]
   > Функция иерархического пространства имен в учетной записи хранения блочных BLOB-объектов доступна в общедоступной предварительной версии и доступна в восточной части США, Восточная часть США 2, Центральная часть США, Юго-Центральный регион США, Западная часть США 2, южная часть Соединенного Королевства, Центральная Канада и Восточная Австралия регионах. Сведения об ограничениях см. [в разделе функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-blob-storage-features.md) и [известных проблемах](data-lake-storage-known-issues.md). Чтобы зарегистрироваться в предварительной версии, см. [эту форму](https://aka.ms/adlspremiumonboard).

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы создать учетную запись блочного BLOB-объекта с помощью Azure CLI, необходимо сначала установить Azure CLI v. 2.0.46 или более поздняя версия. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Войдите в подписку Azure.

   ```azurecli
   az login
   ```

2. При необходимости создайте новую группу ресурсов. Замените значения в квадратных скобках (включая квадратные скобки) и выполните следующую команду.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. Создайте учетную запись Блоккблобстораже. Замените значения в квадратных скобках (включая квадратные скобки) и выполните следующую команду.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   Если вы хотите оптимизировать учетную запись хранения для аналитики данных, добавьте ее `--hierarchical-namespace true` в команду. Дополнительные сведения см. в статье Общие сведения о [Azure Data Lake Storage 2-го поколения](data-lake-storage-introduction.md).

   > [!NOTE]
   > Функция иерархического пространства имен в учетной записи хранения блочных BLOB-объектов доступна в общедоступной предварительной версии и доступна в восточной части США, Восточная часть США 2, Центральная часть США, Юго-Центральный регион США, Западная часть США 2, южная часть Соединенного Королевства, Центральная Канада и Восточная Австралия регионах. Сведения об ограничениях см. [в разделе функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-blob-storage-features.md) и [известных проблемах](data-lake-storage-known-issues.md). Чтобы зарегистрироваться в предварительной версии, см. [эту форму](https://aka.ms/adlspremiumonboard).
   
---

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об учетных записях хранения см. в статье [Общие сведения об учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
