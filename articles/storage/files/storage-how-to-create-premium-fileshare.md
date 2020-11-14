---
title: Создание файлового ресурса Azure уровня "Премиум"
description: Узнайте, как создать общую папку Azure Premium с помощью портал Azure, Azure PowerShell модуля или Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626798"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Создание общей папки Azure Premium

Общие файловые ресурсы уровня "Премиум" предоставляются на носителях с твердотельным накопителем (SSD) и полезны для рабочих нагрузок, интенсивно использующих ввод-вывод, включая размещение баз данных и высокопроизводительных вычислений (HPC). Общие файловые ресурсы уровня "Премиум" размещаются в специальном типе учетной записи хранения, который называется учетной записью Филестораже. Файловые ресурсы уровня "Премиум" предназначены для высокопроизводительных и масштабируемых приложений, обеспечивая постоянную задержку, высокую скорость операций ввода-вывода и высокую пропускную способность.

В этой статье показано, как создать новый тип учетной записи с помощью [портал Azure](https://portal.azure.com/), Azure PowerShell модуля и Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Если вы хотите использовать Azure CLI, [установите последнюю версию](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Если вы планируете использовать модуль Azure PowerShell, [установите последнюю версию](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Создание учетной записи хранения Филестораже

Каждая учетная запись хранения должна принадлежать группе ресурсов Azure. Группа ресурсов — это логический контейнер для группирования служб Azure. При создании учетной записи хранения у вас есть возможность создать новую или использовать существующую группу ресурсов. Для файловых ресурсов Premium требуется учетная запись Филестораже.

# <a name="portal"></a>[Портал](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com/).

Теперь все готово для создания учетной записи хранения.

1. В портал Azure выберите **учетные записи хранения** в меню слева.

    ![портал Azure главной страницы выберите учетную запись хранения](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. В появившемся окне **Учетные записи хранения** выберите **добавить**.
1. Выберите подписку, в которой будет создана учетная запись хранения.
1. В поле **Группа ресурсов** выберите **Создать**. Введите имя для новой группы ресурсов, как показано на следующем рисунке.

1. Далее введите имя своей учетной записи хранения. Выбранное вами имя должно быть уникальным в Azure. Также имя должно содержать от 3 до 24 символов и может состоять только из цифр и строчных букв.
1. Выберите расположение учетной записи хранения или используйте расположение по умолчанию.
1. Для **повышения производительности** выберите **Premium**.

    В раскрывающемся списке **тип учетной записи** выберите значение **Premium** для **филестораже** , чтобы стать доступным.

1. Выберите **тип учетной записи** и щелкните **филестораже**.
1. Оставьте для параметра **репликация** значение по умолчанию **локально избыточное хранилище (LRS)**.

    ![Создание учетной записи хранения для файлового ресурса уровня "Премиум"](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Выберите **Просмотр и создание** , чтобы просмотреть настройки учетной записи хранения и создать учетную запись.
1. Выберите **Создать**.

После создания ресурса учетной записи хранения перейдите к нему.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Вход в Azure

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

### <a name="create-a-filestorage-storage-account"></a>Создание учетной записи хранения Филестораже

Чтобы создать учетную запись хранения Филестораже из PowerShell, используйте команду [New-азсторажеаккаунт](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы запустить Azure Cloud Shell, войдите на [портал Azure](https://portal.azure.com).

Если вы хотите войти в локальную установку интерфейса командной строки, убедитесь, что у вас установлена последняя версия, а затем выполните вход:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов с помощью Azure CLI, используйте команду [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Создание учетной записи хранения Филестораже

Чтобы создать учетную запись хранения Филестораже из Azure CLI, используйте команду [AZ Storage Account Create](/cli/azure/storage/account) .

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Получение ключа учетной записи хранения

Ключи учетной записи хранения контролируют доступ к ресурсам в учетной записи хранения. в этой статье мы используем ключ, чтобы создать файловый ресурс уровня "Премиум". Эти ключи автоматически создаются при создании учетной записи хранения. Ключи учетной записи хранения можно получить с помощью команды [az storage account keys list](/cli/azure/storage/account/keys).

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>Создание общей папки (цен. категория "Премиум")

Теперь, когда вы создали учетную запись Филестораже, вы можете создать файловый ресурс уровня "Премиум" в этой учетной записи хранения.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. В меню слева для учетной записи хранения перейдите к разделу **Файловая служба** , а затем выберите **файлы**.
1. Выберите Общая **Папка** , чтобы создать общую папку Premium.
1. Введите имя и требуемую квоту для общей папки, а затем нажмите кнопку **создать**.

> [!NOTE]
> Подготовленные размеры общих ресурсов задаются квотой общего доступа. Общие ресурсы выставляются по подготовленному размеру. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Создание общей папки (цен. категория "Премиум")](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы создать файловый ресурс уровня "Премиум" с помощью модуля Azure PowerShell, используйте командлет [New-азсторажешаре](/powershell/module/az.storage/New-AzStorageShare) .

> [!NOTE]
> Подготовленные размеры общих ресурсов задаются квотой общего доступа. Общие ресурсы выставляются по подготовленному размеру. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы создать общую папку уровня "Премиум" с помощью Azure CLI, используйте команду [AZ Storage Share Create](/cli/azure/storage/share) .

> [!NOTE]
> Подготовленные размеры общих ресурсов задаются квотой общего доступа. Общие ресурсы выставляются по подготовленному размеру. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Очистка ресурсов

# <a name="portal"></a>[Портал](#tab/azure-portal)

Если вы хотите очистить ресурсы, созданные в этой статье, удалите группу ресурсов. При удалении группы ресурсов также удаляется связанная учетная запись хранения, а также другие ресурсы, связанные с группой ресурсов.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Если вы хотите очистить ресурсы, созданные в этой статье, удалите группу ресурсов. При удалении группы ресурсов также удаляется связанная учетная запись хранения, а также другие ресурсы, связанные с группой ресурсов.

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Если вы хотите очистить ресурсы, созданные в этой статье, удалите группу ресурсов. При удалении группы ресурсов также удаляется связанная учетная запись хранения, а также другие ресурсы, связанные с группой ресурсов.

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали файловый ресурс уровня "Премиум". Чтобы узнать о производительности, предлагаемой этой учетной записью, перейдите к разделу "уровень производительности" в разделе "планирование по планированию".

> [!div class="nextstepaction"]
> [Уровни файлового ресурса](storage-files-planning.md#storage-tiers)