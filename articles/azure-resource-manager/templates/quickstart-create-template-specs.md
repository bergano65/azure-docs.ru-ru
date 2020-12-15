---
title: Создание и развертывание спецификации шаблона
description: Узнайте, как создать спецификацию шаблона на основе шаблона ARM. Затем разверните спецификацию шаблона в группе ресурсов в подписке.
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518963"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Краткое руководство. Создание и развертывание спецификации шаблона (предварительная версия)

В этом кратком руководстве показано, как упаковать шаблон Azure Resource Manager (шаблон ARM) в [спецификацию шаблона ](template-specs.md), а затем развернуть ее. Спецификация шаблона содержит шаблон ARM, который развертывает учетную запись хранения.

## <a name="prerequisites"></a>Предварительные требования

Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

> [!NOTE]
> Сейчас спецификации шаблонов доступны в предварительной версии. Чтобы использовать их с Azure PowerShell, необходимо установить [версию 5.0.0 или более новую](/powershell/azure/install-az-ps). Чтобы использовать их с Azure CLI, воспользуйтесь [версией 2.14.2 или более новой](/cli/azure/install-azure-cli).

## <a name="create-template-spec"></a>Создание спецификации шаблона

Спецификация шаблона — это тип ресурсов с именем **Microsoft.Resources/templateSpecs**. Чтобы создать спецификацию шаблона, можно использовать портал Azure, Azure PowerShell, Azure CLI или шаблон ARM. В любом случае вам понадобится шаблон ARM, упакованный в спецификацию шаблона.

В PowerShell и CLI шаблон ARM передается команде в качестве параметра. При использовании шаблона ARM, шаблон ARM, который необходимо упаковать в спецификацию шаблона, встроен в определение спецификации шаблона.

Эти параметры представлены ниже.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Войдите на [портал Azure](https://portal.azure.com).
1. В верхней части экрана в поле **Поиск ресурсов, служб и документов** введите **спецификации шаблона**, а затем выберите **Спецификации шаблона**.
1. Выберите **Создание спецификации шаблона**.
1. Введите или выберите следующие значения.

    - **Имя**. Введите имя спецификации шаблона.  Например, **storageSpec**.
    - **Подписка**. Выберите подписку Azure, используемую для создания спецификации шаблона.
    - **Группа ресурсов**.Щелкните **Создать** и введите имя новой группы ресурсов.  Например, **templateSpecRG**.
    - **Расположение**. Выберите расположение группы ресурсов. Например, **Западная часть США 2**.
    - **Версия**. Введите версию спецификации шаблона. Например, **1.0** или **v1.0**.

1. По завершении выберите **Next: Изменить шаблон**.
1. Замените содержимое шаблона следующим кодом JSON:

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    Это шаблон, который будет упакован в спецификации шаблона.
1. Выберите **Review + Create** (Просмотреть и создать).
1. Нажмите кнопку **создания**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Когда вы создаете спецификацию шаблона с помощью PowerShell, вы можете передать ее в локальный шаблон. Скопируйте следующий шаблон и сохраните его локально в файл с именем **azuredeploy.json**. В этом кратком руководстве предполагается, что вы сохранили путь **c:\Templates\azuredeploy.json**, однако можете использовать любой другой путь.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Создайте новую группу ресурсов, которая будет содержать спецификацию шаблона.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Затем создайте спецификацию шаблона в этой группе ресурсов. Новой спецификации шаблона присваивается имя **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Когда вы создаете спецификацию шаблона с помощью CLI, вы можете передать ее в локальный шаблон. Скопируйте следующий шаблон и сохраните его локально в файл с именем **azuredeploy.json**. В этом кратком руководстве предполагается, что вы сохранили путь **c:\Templates\azuredeploy.json**, однако можете использовать любой другой путь.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Создайте новую группу ресурсов, которая будет содержать спецификацию шаблона.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Затем создайте спецификацию шаблона в этой группе ресурсов. Новой спецификации шаблона присваивается имя **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[Шаблон ARM](#tab/azure-resource-manager)

1. Когда вы используете шаблон ARM для создания спецификации шаблона, шаблон внедряется в определение ресурса. Скопируйте следующий шаблон и сохраните его локально с именем **azuredeploy.json**. В этом кратком руководстве предполагается, что вы сохранили путь **c:\Templates\azuredeploy.json**, однако можете использовать любой другой путь.

    > [!NOTE]
    > Во внедренном шаблоне все [выражения шаблона](template-expressions.md) необходимо экранировать с помощью второй левой скобки. Используйте `"[[` вместо `"[`. Массивы JSON по-прежнему используют одинарную левую скобку.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Используйте Azure CLI или PowerShell, чтобы создать группу ресурсов.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Разверните шаблон с помощью Azure CLI или PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Развертывание спецификации шаблона

Теперь можно развернуть спецификацию шаблона. Развертывание спецификации шаблона аналогично развертыванию шаблона, содержащегося в ней, за исключением того, что вы передаете ИД ресурса спецификации шаблона в Azure PowerShell или Azure CLI. Вы используете те же команды развертывания и при необходимости передаете значения параметров для спецификации шаблона.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. На портале Azure откройте группу ресурсов, созданную при выполнении последней процедуры.  Например, **templateSpecRG**.
1. Выберите созданную спецификацию шаблона. Например, **storageSpec**.
1. Выберите **Развернуть**.
1. Введите или выберите следующие значения.

    - **Подписка**. Выберите подписку Azure для создания ресурса.
    - **Группа ресурсов**. Выберите **Создать**, а затем введите **storageRG**.
    - **Тип учетной записи хранения**. Выберите **Standard_GRS**.

    Создайте новую группу ресурсов и разверните в ней шаблон из спецификации шаблона.

1. Выберите **Review + create** (Просмотреть и создать).
1. Нажмите кнопку **создания**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Создайте группу ресурсов, которая будет содержать новую учетную запись хранения.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Получите ИД ресурса спецификации шаблона.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Разверните спецификацию шаблона.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Укажите параметры таким же образом, как и для шаблона ARM. Повторно разверните спецификацию шаблона с параметром для типа учетной записи хранения.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Создайте группу ресурсов, которая будет содержать новую учетную запись хранения.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Получите ИД ресурса спецификации шаблона.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Существует известная проблема при получении идентификатора спецификации шаблона и ее присвоении переменной в Windows PowerShell.

1. Разверните спецификацию шаблона.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Укажите параметры таким же образом, как и для шаблона ARM. Повторно разверните спецификацию шаблона с параметром для типа учетной записи хранения.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[Шаблон ARM](#tab/azure-resource-manager)

1. Скопируйте следующий шаблон и сохраните его локально в файл с именем **storage.json.** .

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Используйте Azure CLI или PowerShell для создания новой группы ресурсов для учетной записи хранения.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Разверните шаблон с помощью Azure CLI или PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Предоставление доступа

Если вы хотите позволить другим пользователям в организации развертывать спецификацию шаблона, необходимо предоставить им доступ на чтение. Роль читателя можно назначить группе Azure AD для группы ресурсов, содержащей спецификации шаблонов, к которым вы хотите предоставить общий доступ. Дополнительные сведения см. в статье [Учебник. Предоставление доступа группам к ресурсам Azure с помощью Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурс, развернутый в этом кратком руководстве, удалите обе созданные группы ресурсов.

1. На портале Azure в меню слева выберите "Группа ресурсов".

1. В поле "Фильтровать по имени" введите имя группы ресурсов (templateSpecRG и storageRG).

1. Выберите имя группы ресурсов.

1. В меню сверху выберите "Удалить группу ресурсов".

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о создании спецификации шаблона, включающей связанные шаблоны, см. в статье [Создание спецификации для связанного шаблона](template-specs-create-linked.md).
