---
title: Создание и развертывание спецификации шаблона
description: Узнайте, как создать спецификацию шаблона на основе шаблона ARM. Затем разверните спецификацию шаблона в группе ресурсов в подписке.
author: tfitzmac
ms.date: 07/20/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: b2667e63f7cac5d1e3ad8475501ff909e8f6f3c1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101305"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Краткое руководство. Создание и развертывание спецификации шаблона (предварительная версия)

В этом кратком руководстве показано, как упаковать шаблон Azure Resource Manager (шаблон ARM) в [спецификацию шаблона ](template-specs.md), а затем развернуть ее. Спецификация шаблона содержит шаблон ARM, который развертывает учетную запись хранения.

## <a name="prerequisites"></a>Предварительные требования

Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

> [!NOTE]
> Сейчас спецификации шаблонов доступны в предварительной версии. Чтобы использовать их, нужно [зарегистрироваться в списке ожидания](https://aka.ms/templateSpecOnboarding).
>
> После одобрения в списке ожидания вы получите инструкции по инсталляции модуля PowerShell (предварительная версия).

## <a name="create-template-spec"></a>Создание спецификации шаблона

Спецификация шаблона — это новый тип ресурсов с именем **Microsoft.Resources/templateSpecs**. Чтобы создать спецификацию шаблона, можно использовать Azure PowerShell или шаблон ARM. В любом случае вам понадобится шаблон ARM, упакованный в спецификацию шаблона.

В PowerShell шаблон ARM передается команде в качестве параметра. При использовании шаблона ARM, шаблон ARM, который необходимо упаковать в спецификацию шаблона, встроен в определение спецификации шаблона.

Эти параметры представлены ниже.

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

   ```powershell
   New-AzTemplateSpec `
     -ResourceGroupName templateSpecRG `
     -Name storageSpec `
     -Version "1.0.0.0" `
     -Location westus2 `
     -TemplateJsonFile "c:\Templates\azuredeploy.json"
   ```

# <a name="arm-template"></a>[Шаблон ARM](#tab/azure-resource-manager)

1. Когда вы используете шаблон ARM для создания спецификации шаблона, шаблон внедряется в определение ресурса. Скопируйте следующий шаблон и сохраните его локально с именем **azuredeploy.json**. В этом кратком руководстве предполагается, что вы сохранили путь **c:\Templates\azuredeploy.json**, однако можете использовать любой другой путь.

   > [!NOTE]
   > Во внедренном шаблоне все левые скобки необходимо экранировать с помощью второй левой скобки. Используйте `[[` вместо `[`.

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
                       "name": "1.0.0.1",
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

   ```azurecli
   az group create \
     --name templateSpecRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. Разверните шаблон с помощью Azure CLI или PowerShell.

   ```azurecli
   az deployment group create \
     --name templateSpecRG \
     --template-file "c:\Templates\azuredeploy.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName templateSpecRG `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

---

## <a name="deploy-template-spec"></a>Развертывание спецификации шаблона

Теперь можно развернуть спецификацию шаблона. Развертывание спецификации шаблона аналогично развертыванию шаблона, содержащегося в ней, за исключением того, что вы передаете ИД ресурса спецификации шаблона. Вы используете те же команды развертывания и при необходимости передаете значения параметров для спецификации шаблона.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Создайте группу ресурсов, которая будет содержать новую учетную запись хранения.

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. Получите ИД ресурса спецификации шаблона.

   ```azurepowershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0.0.0").Version.Id
   ```

1. Разверните спецификацию шаблона.

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName demoRG
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
                       "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0.0.0')]"
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

   ```azurecli
   az group create \
     --name storageRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. Разверните шаблон с помощью Azure CLI или PowerShell.

   ```azurecli
   az deployment group create \
     --name storageRG \
     --template-file "c:\Templates\storage.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName storageRG `
     -TemplateFile "c:\Templates\storage.json"
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
