---
title: Создание учетной записи хранения
titleSuffix: Azure Storage
description: Научитесь создавать учетную запись хранения с помощью портала Azure, Azure PowerShell или Azure CLI. Учетная запись хранения Azure предоставляет уникальное пространство имен в Microsoft Azure для хранения и доступа к вашим данным.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255409"
---
# <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

Учетная запись хранения Azure содержит все объекты данных службы хранилища Azure: большие двоичные объекты, файлы, очереди, таблицы и диски. Учетная запись предоставляет уникальное пространство имен для данных хранения Azure, которое доступно из любой точки мира по адресу HTTP или HTTPS. Данные в учетной записи хранения Azure долговечны и высокодоступны, безопасны и масштабируемы.

В этой статье вы научитесь создавать учетную запись хранения с помощью [портала Azure,](https://portal.azure.com/) [Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)или [шаблона менеджера ресурсов Azure.](../../azure-resource-manager/management/overview.md)  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

# <a name="portal"></a>[Портал](#tab/azure-portal)

Нет.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы создать учетную запись хранения данных Azure с PowerShell, убедитесь, что вы установили модуль Azure PowerShell Az версии 0.7 или позже. Для получения дополнительной [информации см.](/powershell/azure/new-azureps-module-az)

Чтобы найти текущую версию, запустите следующую команду:

```powershell
Get-InstalledModule -Name "Az"
```

Для установки или обновления Azure PowerShell [см.](/powershell/azure/install-Az-ps)

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов:

- Вы можете запускать команды CLI с портала Azure в облачной оболочке Azure.
- Вы можете установить CLI и запустить команды CLI локально.

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Azure CLI предварительно установлен и настроен для использования в вашей учетной записи. Нажмите кнопку **Cloud Shell** в меню в правом верхнем разделе портала Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Кнопка запускает интерактивную оболочку, которую можно использовать для выполнения шагов, изложенных в этой статье:

[![Скриншот, показывающий окно облачной оболочки на портале](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Установка CLI локально

Azure CLI также можно установить и применять локально. Эта статья требует, чтобы вы запускали версию Azure CLI 2.0.4 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

# <a name="template"></a>[Шаблон](#tab/template)

Нет.

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

```azurecli-interactive
az login
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Теперь вы готовы создать учетную запись для хранения данных.

Каждая учетная запись хранения должна принадлежать группе ресурсов Azure. Группа ресурсов — это логический контейнер для группирования служб Azure. При создании учетной записи хранения у вас есть возможность создать новую или использовать существующую группу ресурсов. В этой статье показано, как создать новую группу ресурсов.

Учетная запись хранения **общего назначения версии 2** предоставляет доступ ко всем службам хранилища Azure (большим двоичным объектам, файлам, очередям, таблицам и дискам). Описанные здесь шаги создают учетную запись хранения v2 общего назначения, но шаги по созданию любого типа учетной записи хранилища аналогичны.

# <a name="portal"></a>[Портал](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Сначала создайте группу ресурсов с помощью PowerShell, используя команду [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Если вы не уверены, какой регион необходимо задать для параметра `-Location`, вы можете получить список поддерживаемых регионов для своей подписки, выполнив команду [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location
```

Затем создайте учетную запись хранения v2 общего назначения с георезервным хранилищем (RA-GRS) с помощью команды [New-AzStorageAccount.](/powershell/module/az.storage/New-azStorageAccount) Помните, что имя вашей учетной записи хранения должно быть уникальным в Azure, поэтому замените значение заполнителя в скобках на ваше собственное уникальное значение:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Если вы планируете использовать хранилище озер `-EnableHierarchicalNamespace $True` [данных Azure,](https://azure.microsoft.com/services/storage/data-lake-storage/)включите в этот список параметры.

Чтобы создать учетную запись хранения v2 общего назначения с другой опцией репликации, замените желаемое значение в таблице ниже для параметра **SkuName.**

|Параметр репликации  |Параметр SkuName  |
|---------|---------|
|Локально избыточное хранилище (LRS)     |Standard_LRS         |
|Хранилище, избыточное между зонами (ZRS)     |Standard_ZRS         |
|Геоизбыточное хранилище (GRS)     |Standard_GRS         |
|Геоизбыточное хранилище с доступом для чтения (RA-GRS)     |Standard_RAGRS         |
|Гео-зонно-излишнее хранилища (ГЗРС) (предварительный просмотр)    |Standard_GZRS         |
|Чтение-доступ гео-зоны-избыточного хранения (RA-G'S) (предварительный просмотр)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Сначала создайте новую группу ресурсов с помощью Azure CLI, используя команду [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Если вы не уверены, какой регион необходимо задать для параметра `--location`, вы можете получить список поддерживаемых регионов для своей подписки, выполнив команду [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Затем создайте учетную запись хранения v2 общего назначения с георезервным хранилищем с помощью учетной записи [хранения az, создающей](/cli/azure/storage/account#az_storage_account_create) команду. Помните, что имя вашей учетной записи хранения должно быть уникальным в Azure, поэтому замените значение заполнителя в скобках на ваше собственное уникальное значение:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Если вы планируете использовать хранилище озер `--enable-hierarchical-namespace true` [данных Azure,](https://azure.microsoft.com/services/storage/data-lake-storage/)включите в этот список параметры. 

Чтобы создать учетную запись хранения v2 общего назначения с другим вариантом репликации, замените желаемое значение в таблице ниже параметра **sku.**

|Параметр репликации  |параметр sku  |
|---------|---------|
|Локально избыточное хранилище (LRS)     |Standard_LRS         |
|Хранилище, избыточное между зонами (ZRS)     |Standard_ZRS         |
|Геоизбыточное хранилище (GRS)     |Standard_GRS         |
|Геоизбыточное хранилище с доступом для чтения (RA-GRS)     |Standard_RAGRS         |
|Гео-зонно-излишнее хранилища (ГЗРС) (предварительный просмотр)    |Standard_GZRS         |
|Чтение-доступ гео-зоны-избыточного хранения (RA-G'S) (предварительный просмотр)    |Standard_RAGZRS         |

# <a name="template"></a>[Шаблон](#tab/template)

Вы можете использовать Azure Powershell или Azure CLI, чтобы развернуть шаблон Resource Manager для создания учетной записи хранения. Шаблон, используемый в этой статье, — это [шаблоны быстрого запуска ресурсов Azure Resource Manager.](https://azure.microsoft.com/resources/templates/101-storage-account-create/) Чтобы запустить эти сценарии, выберите **Попробовать**, чтобы открыть Azure Cloud Shell. Чтобы вставить сценарий, щелкните правой кнопкой мыши оболочку и выберите **Вставить**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Этот шаблон служит лишь примером. Есть много параметров учетной записи хранилища, которые не настроены как часть этого шаблона. Например, если требуется использовать [хранилище озер данных Azure,](https://azure.microsoft.com/services/storage/data-lake-storage/) `isHnsEnabledad` вы измените этот шаблон, установив свойство `StorageAccountPropertiesCreateParameters` объекта для `true` 

Чтобы узнать, как изменить этот шаблон или создать новые, см.:

- [Документация менеджера ресурсов Azure](/azure/azure-resource-manager/).
- [Microsoft.Storage resource types](/azure/templates/microsoft.storage/allversions) (Типы ресурсов Microsoft.Storage).
- [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Дополнительные сведения о доступных вариантах репликации см. в статье [Репликация службы хранилища Azure](storage-redundancy.md).

## <a name="delete-a-storage-account"></a>Удаление учетной записи хранения

Удаление учетной записи хранилища удаляет всю учетную запись, включая все данные в учетной записи, и не может быть отменено.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Перейдите к учетной записи хранилища на [портале Azure](https://portal.azure.com).
1. Выберите команду **Удалить**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы удалить учетную запись хранилища, используйте команду [Удалить-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы удалить учетную запись хранилища, используйте команду [удаления учетной записи хранилища az:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Шаблон](#tab/template)

Чтобы удалить учетную запись хранения, используйте либо Azure PowerShell, либо Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Кроме того, можно удалить группу ресурсов, которая удаляет учетную запись хранения и любые другие ресурсы этой группы ресурсов. Для получения дополнительной информации об удалении группы ресурсов [см.](../../azure-resource-manager/management/delete-resource-group.md)

> [!WARNING]
> Восстановить удаленную учетную запись хранения или ее содержимое невозможно. Создайте резервные копии нужных данных, прежде чем удалять учетную запись. Это касается также любых ресурсов в учетной записи. Восстановить удаленный BLOB-объект, таблицу, очередь или файл невозможно.
>
> Если вы попытаетесь удалить учетную запись хранения, связанную с виртуальной машиной Azure, может появиться сообщение об ошибке, уведомляющее, что учетная запись хранения используется. Для устранения этой ошибки, сможете устранить [ошибки, когда вы удаляете учетные записи хранилища.](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали стандартную учетную запись хранения v2 общего назначения. Чтобы узнать, как загрузить и загрузить капли в и из вашей учетной записи хранения, продолжить один из Blob хранения quickstarts.

# <a name="portal"></a>[Портал](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Шаблон](#tab/template)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](../blobs/storage-quickstart-blobs-portal.md)

---
