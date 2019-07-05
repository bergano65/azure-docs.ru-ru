---
title: Создайте общую папку файлов Azure уровня "премиум"
description: В этой статье вы узнаете, как создать общую папку файлов Azure уровня "премиум".
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 63caf9a08acb04bab3712891701d32c21c22e9fc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449902"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Как создать общую папку "премиум" Azure
Файловые ресурсы уровня "премиум" доступны на любых носителях твердотельные накопители (SSD) и полезны для ИНТЕНСИВНЫХ по вводу-выводу рабочих нагрузок, включая размещение баз данных и высокопроизводительных вычислений (HPC). Файловые ресурсы уровня "премиум", размещенные в специальных типа учетной записи хранения, называется учетной записью хранилища. Файловые ресурсы уровня "премиум" предназначены для высокой производительности и масштабирования приложений, предоставляя постоянно низкую задержку, высокий уровень операций ввода-ВЫВОДА и общие папки высокой пропускной способностью.

В этой статье показано, как создать этот новый тип учетной записи, используя [портала Azure](https://portal.azure.com/), Azure PowerShell и Azure CLI.

## <a name="prerequisites"></a>Технические условия

Для доступа к ресурсам Azure, включая файловые ресурсы Azure уровня "премиум", вам потребуется подписка Azure. Если у вас еще нет подписки, вы можете [создать бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Создайте файловый ресурс уровня "премиум" с помощью портала Azure

### <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портале Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Создайте учетную запись хранилища

Теперь все готово для создания учетной записи хранения.

Каждая учетная запись хранения должна принадлежать группе ресурсов Azure. Группа ресурсов — это логический контейнер для группирования служб Azure. При создании учетной записи хранения у вас есть возможность создать новую или использовать существующую группу ресурсов. В этой статье показано, как создать группу ресурсов.

1. На портале Azure выберите **учетные записи хранения** в меню слева.

    ![Главной странице портала Azure выберите учетную запись хранения](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. В появившемся окне **Учетные записи хранения** выберите **добавить**.
1. Выберите подписку, в которой будет создана учетная запись хранения.
1. В поле **Группа ресурсов** выберите **Создать**. Введите имя для новой группы ресурсов, как показано на следующем рисунке.

1. Далее введите имя своей учетной записи хранения. Выбранное вами имя должно быть уникальным в Azure. Также имя должно содержать от 3 до 24 символов и может состоять только из цифр и строчных букв.
1. Выберите расположение учетной записи хранения или используйте расположение по умолчанию.
1. Для **производительности** выберите **уровня "премиум"** .
1. Выберите **тип учетной записи** и выберите **хранилища**.
1. Оставьте **репликации** присвоено значение по умолчанию **локально избыточное хранилище (LRS)** .

    ![Как создать учетную запись хранения для общей папки "премиум"](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Выберите **Просмотр и создание**, чтобы просмотреть настройки учетной записи хранения и создать учетную запись.
1. Нажмите кнопку **Создать**.

Когда ресурс учетной записи хранения будет создана, перейдите к нему.

### <a name="create-a-premium-file-share"></a>Создание общей папки (цен. категория "Премиум")

1. В меню слева для учетной записи хранения, перейдите к **файловой службы** раздела, а затем выберите **файлы**.
1. Выберите **+ файловый ресурс** для создания общей папки "премиум".
1. Введите имя и необходимой квоты для вашего файлового ресурса, а затем выберите **создать**.

> [!NOTE]
> Размеры подготовленного общего файлового ресурса задается в квоте общих ресурсов, файловые ресурсы оплачиваются на подготовленном размере см. [странице с ценами](https://azure.microsoft.com/pricing/details/storage/files/) для получения дополнительных сведений.

   ![Создание общей папки (цен. категория "Премиум")](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить ресурсы, созданные в этой статье, можно просто удалить группу ресурсов. При удалении группы ресурсов также удаляются, соответствующей учетной записи, а также другие ресурсы, связанные с группой ресурсов.

## <a name="create-a-premium-file-share-using-powershell"></a>Создайте файловый ресурс уровня "премиум" с помощью PowerShell

### <a name="create-an-account-using-powershell"></a>Создание учетной записи с помощью PowerShell

Сначала установите последнюю версию модуля [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).

Затем обновите модуль PowerShell, войдите в подписку Azure, создайте группу ресурсов и учетную запись хранения.

### <a name="upgrade-your-powershell-module"></a>Обновление модуля PowerShell

Для взаимодействия с уровня "премиум" общей папки с помощью PowerShell, необходимо установить версию модуля Az.Storage 1.4.0 или последнюю версию модуля Az.Storage.

Сначала откройте сеанс PowerShell с повышенными разрешениями.

Установите модуль Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Вход в подписку Azure

Чтобы выполнить проверку подлинности, используйте команду `Connect-AzAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов с помощью PowerShell, выполните команду [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Создайте учетную запись хранилища

Чтобы создать учетную запись хранилища с помощью PowerShell, используйте [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) команды:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Создание общей папки (цен. категория "Премиум")

Теперь у вас учетной записи хранилища, можно создать общую папку "премиум". Используйте [New AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) командлет, чтобы создать его.

> [!NOTE]
> Размеры подготовленного общего файлового ресурса задается в квоте общих ресурсов, файловые ресурсы оплачиваются на подготовленном размере см. [странице с ценами](https://azure.microsoft.com/pricing/details/storage/files/) для получения дополнительных сведений.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Создайте файловый ресурс уровня "премиум" с помощью Azure CLI

Чтобы запустить Azure Cloud Shell, войдите на [портал Azure](https://portal.azure.com).

Если вы хотите войти в локальную установку CLI, сначала убедитесь, что вы используете последнюю версию, выполните команду для входа:

```cli
az login
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов с помощью Azure CLI, используйте команду [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Создайте учетную запись хранилища

Чтобы создать учетную запись хранилища с помощью Azure CLI, используйте [Создание учетной записи хранения az](/cli/azure/storage/account) команды.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Получение ключа учетной записи хранения

Ключи учетной записи хранения контроля доступа к ресурсам в учетной записи хранения в этой статье, мы используем ключ, чтобы создать файловый ресурс уровня "премиум". Эти ключи автоматически создаются при создании учетной записи хранения. Ключи учетной записи хранения можно получить с помощью команды [az storage account keys list](/cli/azure/storage/account/keys).

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Создание общей папки (цен. категория "Премиум")

Теперь у вас учетной записи хранилища, можно создать общую папку "премиум". Используйте [создать общую папку хранилища az](/cli/azure/storage/share) команду, чтобы создать его.

> [!NOTE]
> Размеры подготовленного общего файлового ресурса задается в квоте общих ресурсов, файловые ресурсы оплачиваются на подготовленном размере см. [странице с ценами](https://azure.microsoft.com/pricing/details/storage/files/) для получения дополнительных сведений.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали общую папку "премиум". Дополнительные сведения о производительности, которые предлагает эта учетная запись, перейдите к разделу уровня производительности руководства по планированию.

> [!div class="nextstepaction"]
> [Файл уровни производительности в общую папку](storage-files-planning.md#file-share-performance-tiers)