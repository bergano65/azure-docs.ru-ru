---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 29a31948c5bfc9b5fd3a31f39144d186dbfe4885
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026197"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Создание вспомогательных ресурсов Azure для функции

Прежде чем развернуть код функции в Azure, необходимо создать три ресурса:

- группу ресурсов — логический контейнер связанных ресурсов;
- учетную запись хранения — для сохранения состояния и других сведений о проектах;
- приложение-функцию — среду для выполнения кода функции. Оно сопоставляется с локальным проектом функций и позволяет группировать функции в логические единицы, чтобы упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

Чтобы создать эти элементы, выполните следующие команды: Поддерживается Azure CLI и PowerShell.

1. Войдите в Azure, если вы еще этого не сделали:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Чтобы войти в учетную запись Azure, выполните команду [az login](/cli/azure/reference-index#az_login).

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Чтобы войти в учетную запись Azure, выполните командлет [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

    ---

1. Создайте группу ресурсов с именем `AzureFunctionsQuickstart-rg` в регионе `westeurope`:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Чтобы создать группу ресурсов, выполните команду [az group create](/cli/azure/group#az_group_create). Группу ресурсов и ресурсы целесообразно создавать в ближайшем к вам регионе. Для этого используйте команду `az account list-locations`.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Команда [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) создает группу ресурсов. Группу ресурсов и ресурсы целесообразно создавать в ближайшем к вам регионе. Для этого используйте командлет [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

1. В группе ресурсов и регионе создайте учетную запись хранения общего назначения:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Создайте учетную запись хранения с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Чтобы создать учетную запись хранения, выполните командлет [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ---

    В предыдущем примере замените `<STORAGE_NAME>` соответствующим именем, которое является уникальным в службе хранилища Azure. Имена должны содержать от трех до 24 символов и только в нижнем регистре. `Standard_LRS` указывает учетную запись общего назначения, которая [поддерживается Функциями](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
