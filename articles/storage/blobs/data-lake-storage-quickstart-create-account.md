---
title: Создание учетной записи хранения Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Здесь показано, как быстро создать учетную запись хранения с доступом к Azure Data Lake Storage 2-го поколения с использованием портала Azure, Azure PowerShell или Azure CLI.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 2063dd22e3253b0707f6920f3a5c0c7a6bb01126
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992316"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Создание поддерживаемой учетной записи хранения Azure Data Lake Storage 2-го поколения

Azure Data Lake Storage 2-го поколения [поддерживает иерархическое пространство имен](data-lake-storage-introduction.md), которое предоставляет собственный контейнер на основе каталогов, предназначенный для работы с распределенной файловой системой Hadoop (HDFS). Доступ к данным Data Lake Storage Gen2 из распределенной файловой системы Hadoop доступен с помощью [драйвера ABFS](data-lake-storage-abfs-driver.md).

В этом кратком руководстве показано, как создать учетную запись с помощью [портала Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) или [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу. 

|           | Предварительные требования |
|-----------|--------------|
|Портал     | Нет         |
|PowerShell | Для работы с этим кратким руководством требуется модуль PowerShell Az.Storage версии **0.7** или более поздней. Чтобы узнать, какая версия используется сейчас, выполните команду `Get-Module -ListAvailable Az.Storage`. Если после выполнения этой команды результаты не отображаются или отображается версия, предшествующая **0.7**, тогда вам нужно обновить модуль PowerShell. Дополнительные сведения см. в разделе [Обновление модуля PowerShell](#upgrade-your-powershell-module) этого руководства.
|CLI        | Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов: <ul><li>Выполнить команды CLI на портале Azure в Azure Cloud Shell. </li><li>Установить CLI и выполнить команды CLI локально.</li></ul>|

При работе в командной строке можно запустить оболочку Azure Cloud или локально установить интерфейс командной строки.

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна портала Azure.

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Эта кнопка запускает интерактивную оболочку, с помощью которой можно выполнять действия, описанные в этом кратком руководстве:

[![Снимок экрана с окном Azure Cloud Shell на портале](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Установка CLI локально

Azure CLI также можно установить и применять локально. Для этого руководства требуется Azure CLI 2.0.38 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Создание учетной записи хранения с поддержкой Azure Data Lake Storage 2-го поколения

Прежде чем создать учетную запись, сначала создайте группу ресурсов, которая будет исполнять роль логического контейнера для хранения учетных записей или других ресурсов Azure, которые вы создаете. Если нужно очистить ресурсы, созданные при работе с этим кратким руководством, можно просто удалить группу ресурсов. При этом удаляется связанная учетная запись хранения и другие ресурсы, связанные с этой группой ресурсов. Дополнительные сведения о группах ресурсов см. в статье [Обзор Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Чтобы воспользоваться преимуществами функций Data Lake Storage Gen2, необходимо создать новые учетные записи хранения типа **StorageV2 (общего назначения V2)** .  

Дополнительные сведения об учетных записях хранения см. в статье [Общие сведения об учетной записи хранения](../common/storage-account-overview.md).

Помните о следующих правилах при назначении имени учетной записи хранения.

- Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.
- Имя учетной записи хранения должно быть уникальным в Azure. Две учетные записи хранения не могут иметь одно имя.

## <a name="create-an-account-using-the-azure-portal"></a>Создание учетной записи с использованием портала Azure

Войдите на [портале Azure](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов на портале Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов).
2. Нажмите кнопку **Add** (Добавить), чтобы добавить новую группу ресурсов.
3. Введите имя для группы ресурсов.
4. Выберите подписку, в которой нужно создать группу ресурсов.
5. Выберите расположение группы ресурсов.
6. Нажмите кнопку **Создать** .  

   ![Снимок экрана, на котором показано создание группы ресурсов на портале Azure](./media/data-lake-storage-quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Создание учетной записи хранения общего назначения v2

Чтобы создать учетную запись хранения общего назначения версии 2 на портале Azure, сделайте следующее:

> [!NOTE]
> Иерархическое пространство имен сейчас доступно во всех общедоступных регионах.

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Все службы**. Прокрутите вниз до пункта **Хранилище** и выберите **Учетные записи хранения**. В появившемся окне **Учетные записи хранения** выберите **добавить**.
2. Выберите **подписку** и **группу ресурсов**, созданные ранее.
3. Выберите имя для своей учетной записи хранения.
4. Задайте параметру **Расположение** значение **Западный регион США 2**.
5. Оставьте значения по умолчанию в следующих полях: **Производительность**, **Тип учетной записи**, **Репликация**, **Уровень доступа**.
6. Выберите подписку, в которой нужно создать учетную запись хранения.
7. Выберите **Далее: Дополнительно >** .
8. Оставьте значения по умолчанию в полях **Безопасность** и **Виртуальные сети**.
9. В разделе **Data Lake Storage Gen2** задайте параметру **Иерархическое пространство имен** состояние **Включено**.
10. Щелкните **Review + Create** (Просмотр и создание), чтобы создать учетную запись хранения.

    ![Снимок экрана, на котором показано создание учетной записи хранения на портале Azure](./media/data-lake-storage-quickstart-create-account/azure-data-lake-storage-account-create-advanced.png)

Теперь учетная запись хранилища создана с помощью портала.

### <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов с помощью портала Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов), чтобы просмотреть список групп ресурсов.
2. Найдите группу ресурсов, которую нужно удалить, и щелкните правой кнопкой мыши кнопку **More** (Дополнительно) ( **...** ) справа от списка.
3. Выберите **Удалить группу ресурсов** и подтвердите выбор.

## <a name="create-an-account-using-powershell"></a>Создание учетной записи с помощью PowerShell

Сначала установите последнюю версию модуля [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).

Затем обновите модуль PowerShell, войдите в подписку Azure, создайте группу ресурсов и учетную запись хранения.

### <a name="upgrade-your-powershell-module"></a>Обновление модуля powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Для взаимодействия с Data Lake Storage 2-го поколения с помощью PowerShell вам требуется установить модуль Az.Storage версии **0.7** или более поздней.

Сначала откройте сеанс PowerShell с повышенными разрешениями.

Установка модуля Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Вход в подписку Azure

Чтобы выполнить проверку подлинности, используйте команду `Login-AzAccount` и следуйте инструкциям на экране.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов с помощью PowerShell, выполните команду [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

> [!NOTE]
> Иерархическое пространство имен сейчас доступно во всех общедоступных регионах.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Создание учетной записи хранения общего назначения v2

Чтобы создать учетную запись хранения общего назначения версии 2 с локально избыточным хранилищем (LRS) с помощью PowerShell, используйте команду [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount).

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Создание учетной записи с помощью Azure CLI

Чтобы запустить Azure Cloud Shell, войдите на [портал Azure](https://portal.azure.com).

Если вы хотите войти в локальную установку CLI, выполните команду входа:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Добавление расширения CLI для Azure Data Lake 2-го поколения

Чтобы взаимодействовать с Data Lake Storage 2-го поколения через CLI, добавьте расширение в оболочку.

Для этого с помощью Cloud Shell или локальный оболочки введите следующую команду: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов с помощью Azure CLI, используйте команду [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > Иерархическое пространство имен сейчас доступно во всех общедоступных регионах.

### <a name="create-a-general-purpose-v2-storage-account"></a>Создание учетной записи хранения общего назначения v2

Чтобы создать учетную запись хранения общего назначения версии 2 с локально избыточным хранилищем с помощью Azure CLI, примените команду [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали учетную запись хранения с поддержкой Data Lake Storage 2-го поколения. Сведения об отправке и скачивании больших двоичных объектов в учетную запись хранения и из нее см. в следующей статье.

* [Передача данных с помощью AzCopy версии 10 (предварительная версия)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
