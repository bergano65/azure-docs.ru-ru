---
title: Создание учетной записи хранения (служба хранилища Azure)
description: В этом пошаговом руководстве вы узнаете, как создать учетную запись хранения с помощью портал Azure, Azure PowerShell или Azure CLI. Учетная запись хранения Azure предоставляет уникальное пространство имен в Microsoft Azure для хранения объектов данных в службе хранилища Azure и предоставления доступа к ним.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 338ba468fb1442488da78f09fc72a22186cd0a51
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673222"
---
# <a name="create-a-storage-account"></a>Создать учетную запись хранения

Учетная запись хранения Azure содержит все объекты данных службы хранилища Azure: большие двоичные объекты, файлы, очереди, таблицы и диски. Учетная запись хранения предоставляет уникальное пространство имен для данных службы хранилища Azure, доступное из любой точки мира по протоколу HTTP или HTTPS. Данные в учетной записи хранения Azure являются устойчивыми и высокодоступными, безопасными и масштабируемыми.

В этом пошаговом руководстве вы узнаете, как создать учетную запись хранения с помощью [портал Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)или [шаблона Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Нет.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Для работы с этой статьей требуется модуль Azure PowerShell AZ версии 0,7 или более поздней. Чтобы узнать, какая версия используется сейчас, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов:

- Команды интерфейса командной строки можно выполнять в портал Azure в Azure Cloud Shell.
- Вы можете установить интерфейс командной строки и выполнить команды CLI локально.

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Azure CLI предварительно установлен и настроен для использования с вашей учетной записью. Нажмите кнопку **Cloud Shell** в меню в правой верхней части портал Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Кнопка запускает интерактивную оболочку, которую можно использовать для выполнения действий, описанных в этой статье.

[![Снимок экрана с окном Azure Cloud Shell на портале](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Установка CLI локально

Azure CLI также можно установить и применять локально. В этом руководстве требуется, чтобы вы выполняли Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Шаблон](#tab/template)

Нет.

---

## <a name="sign-in-to-azure"></a>Вход в Azure

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Войдите на [портале Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнить проверку подлинности, войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Чтобы запустить Azure Cloud Shell, войдите в [портал Azure](https://portal.azure.com).

Чтобы войти в локальную установку интерфейса командной строки, выполните команду [AZ login](/cli/azure/reference-index#az-login) :

```cli
az login
```

# <a name="templatetabtemplate"></a>[Шаблон](#tab/template)

Н/Д

---

## <a name="create-a-storage-account"></a>Создать учетную запись хранения

Теперь вы готовы создать свою учетную запись.

Каждая учетная запись хранения должна принадлежать группе ресурсов Azure. Группа ресурсов — это логический контейнер для группирования служб Azure. При создании учетной записи хранения у вас есть возможность создать новую или использовать существующую группу ресурсов. В этой статье показано, как создать новую группу ресурсов.

Учетная запись хранения **общего назначения версии 2** предоставляет доступ ко всем службам хранилища Azure (большим двоичным объектам, файлам, очередям, таблицам и дискам). Шаги, описанные здесь, позволяют создать учетную запись хранения общего назначения версии 2, но действия по созданию учетной записи хранения любого типа похожи.

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Сначала создайте группу ресурсов с помощью PowerShell, используя команду [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Если вы не уверены, какой регион необходимо задать для параметра `-Location`, вы можете получить список поддерживаемых регионов для своей подписки, выполнив команду [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Затем создайте учетную запись хранения общего назначения версии 2 с геоизбыточным хранилищем с доступом на чтение (RA-GRS) с помощью команды [New-азсторажеаккаунт](/powershell/module/az.storage/New-azStorageAccount) . Помните, что имя вашей учетной записи хранения должно быть уникальным в пределах Azure, поэтому замените значение заполнителя в квадратных скобках своим уникальным значением:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Чтобы создать учетную запись хранения общего назначения версии 2 с другим вариантом репликации, замените требуемое значение в таблице ниже для параметра **SkuName** .

|Параметр репликации  |Параметр SkuName  |
|---------|---------|
|Локально избыточное хранилище (LRS)     |Standard_LRS         |
|Хранилище, избыточное между зонами (ZRS)     |Standard_ZRS         |
|Геоизбыточное хранилище (GRS)     |Standard_GRS         |
|Геоизбыточное хранилище с доступом для чтения (RA-GRS)     |Standard_RAGRS         |
|Хранилище, избыточное в геопоясе (ГЗРС) (Предварительная версия)    |Standard_GZRS         |
|Хранилище, избыточное в геозону с доступом на чтение (RA-ГЗРС) (Предварительная версия)    |Standard_RAGZRS         |

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

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

Затем создайте учетную запись хранения общего назначения версии 2 с геоизбыточным хранилищем с доступом на чтение с помощью команды [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . Помните, что имя вашей учетной записи хранения должно быть уникальным в пределах Azure, поэтому замените значение заполнителя в квадратных скобках своим уникальным значением:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Чтобы создать учетную запись хранения общего назначения версии 2 с другим вариантом репликации, замените требуемое значение в таблице ниже для параметра **SKU** .

|Параметр репликации  |параметр sku  |
|---------|---------|
|Локально избыточное хранилище (LRS)     |Standard_LRS         |
|Хранилище, избыточное между зонами (ZRS)     |Standard_ZRS         |
|Геоизбыточное хранилище (GRS)     |Standard_GRS         |
|Геоизбыточное хранилище с доступом для чтения (RA-GRS)     |Standard_RAGRS         |
|Хранилище, избыточное в геопоясе (ГЗРС) (Предварительная версия)    |Standard_GZRS         |
|Хранилище, избыточное в геозону с доступом на чтение (RA-ГЗРС) (Предварительная версия)    |Standard_RAGZRS         |

# <a name="templatetabtemplate"></a>[Шаблон](#tab/template)

Вы можете использовать Azure Powershell или Azure CLI, чтобы развернуть шаблон Resource Manager для создания учетной записи хранения. Шаблон, используемый в этой статье, относится к [Azure Resource Manager шаблонам](https://azure.microsoft.com/resources/templates/101-storage-account-create/)быстрого запуска. Чтобы запустить эти сценарии, выберите **Попробовать**, чтобы открыть Azure Cloud Shell. Чтобы вставить сценарий, щелкните правой кнопкой мыши оболочку и выберите **Вставить**.

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

Чтобы узнать, как создавать шаблоны, см. следующие статьи:

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/).
- [Microsoft.Storage resource types](/azure/templates/microsoft.storage/allversions) (Типы ресурсов Microsoft.Storage).
- [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Дополнительные сведения о доступных вариантах репликации см. в статье [Репликация службы хранилища Azure](storage-redundancy.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить ресурсы, созданные в этой статье, можно удалить группу ресурсов. При этом удаляется связанная учетная запись хранения и другие ресурсы, связанные с этой группой ресурсов.

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Чтобы удалить группу ресурсов с помощью портала Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов), чтобы просмотреть список групп ресурсов.
2. Найдите группу ресурсов, которую нужно удалить, и щелкните правой кнопкой мыши кнопку **More** (Дополнительно) ( **...** ) справа от списка.
3. Выберите **Удалить группу ресурсов** и подтвердите выбор.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Шаблон](#tab/template)

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте Azure PowerShell или Azure CLI.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Следующие шаги

В этом пошаговом руководстве вы создали стандартную учетную запись хранения общего назначения версии 2. Чтобы узнать, как отправлять и скачивать большие двоичные объекты в учетной записи хранения, перейдите к одному из кратких руководств по хранилищу BLOB-объектов.

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Шаблон](#tab/template)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](../blobs/storage-quickstart-blobs-portal.md)

---
