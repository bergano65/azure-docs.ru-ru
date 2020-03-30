---
title: Создание премиум-раздела файлов Azure
description: В этой статье вы узнаете, как создать премиум-файл Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529113"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Как создать премиум-раздел файла Azure
Премиум-файлы предлагаются на твердотельных дисках (SSD) и полезны для io-интенсивных рабочих нагрузок, включая хостинг баз данных и высокопроизводительные вычисления (HPC). Премиум-файлы размещаются в специальном виде учетной записи хранения, называемой учетной записью FileStorage. Премиум-файлы предназначены для приложений высокой производительности и корпоративного масштаба, обеспечивая постоянную низкую задержку, высокую IOPS и высокую пропускную способность.

В этой статье показано, как создать новый тип учетной записи с помощью [портала Azure,](https://portal.azure.com/)Azure PowerShell и Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

Для доступа к ресурсам Azure, включая премиум-файлы Azure, потребуется подписка Azure. Если у вас еще нет подписки, вы можете [создать бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Создание премиум-файла с помощью портала Azure

### <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Создание учетной записи хранения файлов

Теперь вы готовы создать учетную запись хранения.

Каждая учетная запись хранения должна принадлежать группе ресурсов Azure. Группа ресурсов — это логический контейнер для группирования служб Azure. При создании учетной записи хранения у вас есть возможность создать новую или использовать существующую группу ресурсов. В этой статье показано, как создать новую группу ресурсов.

1. На портале Azure выберите **учетные записи хранения** в левом меню.

    ![Главная страница портала Azure выберите учетную запись хранения](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. В появившемся окне **Учетные записи хранения** выберите **добавить**.
1. Выберите подписку, в которой будет создана учетная запись хранения.
1. В поле **Группа ресурсов** выберите **Создать**. Введите имя для новой группы ресурсов, как показано на следующем рисунке.

1. Далее введите имя своей учетной записи хранения. Выбранное вами имя должно быть уникальным в Azure. Также имя должно содержать от 3 до 24 символов и может состоять только из цифр и строчных букв.
1. Выберите расположение учетной записи хранения или используйте расположение по умолчанию.
1. Для **производительности** выберите **Premium**.
1. Выберите **вид учетной записи** и выберите **FileStorage**.
1. Оставьте **репликацию** в значении локального **избыточного хранилища (LRS) локально-избыточного хранилища (LRS).**

    ![Как создать учетную запись хранения для премиум-файла](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Выберите **Просмотр и создание**, чтобы просмотреть настройки учетной записи хранения и создать учетную запись.
1. Выберите **Создать**.

Как только ресурс учетной записи хранилища был создан, перейдите к нему.

### <a name="create-a-premium-file-share"></a>Создание общей папки (цен. категория "Премиум")

1. В левом меню для учетной записи хранилища прокрутите раздел **Службы файлов,** а затем выберите **Файлы.**
1. Выберите **долю файла** для создания премиум-файла.
1. Введите имя и нужную квоту для вашей доли файла, а затем выберите **Создать**.

> [!NOTE]
> Предусмотренные размеры акций определяются квотой акций, файлы выставляются на предусмотренный размер, относятся к [странице ценообразования](https://azure.microsoft.com/pricing/details/storage/files/) для более подробной информации.

   ![Создание общей папки (цен. категория "Премиум")](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить ресурсы, созданные в этой статье, вы можете просто удалить группу ресурсов. Удаление группы ресурсов также удаляет связанную учетную запись хранения, а также любые другие ресурсы, связанные с группой ресурсов.

## <a name="create-a-premium-file-share-using-powershell"></a>Создание премиум-файла с помощью PowerShell

### <a name="create-an-account-using-powershell"></a>Создание учетной записи с помощью PowerShell

Сначала установите последнюю версию модуля [PowerShellGet](/powershell/scripting/gallery/installing-psget).

Затем обновите модуль PowerShell, вопийте подписку На Azure, создайте группу ресурсов, а затем создайте учетную запись хранения.

### <a name="upgrade-your-powershell-module"></a>Обновление модуля PowerShell

Чтобы взаимодействовать с премиум-файлом с PowerShell, необходимо установить версию модуля Az.Storage 1.4.0 или последний модуль Az.Storage.

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

### <a name="create-a-filestorage-storage-account"></a>Создание учетной записи хранения файлов

Чтобы создать учетную запись хранения файлов из PowerShell, используйте команду [New-AzStorageAccount:](/powershell/module/az.storage/New-azStorageAccount)

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Создание общей папки (цен. категория "Премиум")

Теперь, когда у вас есть учетная запись FileStorage, вы можете создать премиум-файл. Для создания cmdlet [New-AzStorageShare используйте cmdlet New-AzStorageShare.](/powershell/module/az.storage/New-AzStorageShare)

> [!NOTE]
> Предусмотренные размеры акций определяются квотой акций, файлы выставляются на предусмотренный размер, относятся к [странице ценообразования](https://azure.microsoft.com/pricing/details/storage/files/) для более подробной информации.

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>Создание премиум-файла с помощью Azure CLI

Чтобы запустить Azure Cloud Shell, войдите на [портал Azure](https://portal.azure.com).

Если вы хотите войти в локальную установку CLI, сначала убедитесь, что у вас есть последняя версия, а затем запустите команду входа:

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

### <a name="create-a-filestorage-storage-account"></a>Создание учетной записи хранения файлов

Чтобы создать учетную запись хранения FileStorage из Azure CLI, используйте учетную запись [хранения az, создавая команду.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Получение ключа учетной записи хранения

Ключи учетной записи хранения контролируют доступ к ресурсам в учетной записи хранилища, в этой статье мы используем ключ для создания премиум-файла. Эти ключи автоматически создаются при создании учетной записи хранения. Ключи учетной записи хранения можно получить с помощью команды [az storage account keys list](/cli/azure/storage/account/keys).

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Создание общей папки (цен. категория "Премиум")

Теперь, когда у вас есть учетная запись файлохранилища, вы можете создать премиум-файл. Используйте [команду создания раздела хранилища az](/cli/azure/storage/share) для ее создания.

> [!NOTE]
> Предусмотренные размеры акций определяются квотой акций, файлы выставляются на предусмотренный размер, относятся к [странице ценообразования](https://azure.microsoft.com/pricing/details/storage/files/) для более подробной информации.

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

В этой статье вы создали премиум-файл. Чтобы узнать о производительности, который предлагает эта учетная запись, перейти к разделу уровня производительности руководства по планированию.

> [!div class="nextstepaction"]
> [Уровни общего обмена файлами](storage-files-planning.md#storage-tiers)