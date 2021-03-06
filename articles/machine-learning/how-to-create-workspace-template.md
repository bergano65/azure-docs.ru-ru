---
title: Создание рабочей области с помощью шаблона Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать шаблон Azure Resource Manager для создания новой Машинное обучение Azure рабочей области.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 63638dccbe489a6d63d4c1875d68ca12f0015836
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689166"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Создание рабочей области для Машинное обучение Azure с помощью шаблона Azure Resource Manager

Из этой статьи вы узнаете несколько способов создания Машинное обучение Azure рабочей области с помощью шаблонов Azure Resource Manager. Шаблон Resource Manager позволяет легко создать все ресурсы в отдельной скоординированной операции. Шаблон представляет собой документ JSON, в котором определены необходимые для развертывания ресурсы. Также здесь можно указать параметры развертывания. Эти параметры позволят предоставить входные значения при использовании шаблона.

Узнайте подробнее [о развертывании приложения с помощью шаблона диспетчера ресурсов Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. Если у вас ее нет, попробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree).

* Чтобы использовать шаблон из командной строки, вам потребуется [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Следующий шаблон диспетчер ресурсов можно использовать для создания Машинное обучение Azure рабочей области и связанных ресурсов Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Этот шаблон создает следующие ресурсы Azure:

* Группа ресурсов Azure
* Учетная запись хранения Azure
* Azure Key Vault.
* Azure Application Insights
* Реестр контейнеров Azure
* Рабочая область службы "Машинное обучение Azure"

Группа ресурсов выполняет функции контейнера, который содержит службы. Для рабочего пространства Службы машинного обучения Azure нужно несколько служб.

Наш пример шаблона принимает два параметра.

* **Расположение** указывает, где будут созданы группа ресурсов и службы.

    Этот шаблон будет использовать выбранное расположение для большинства ресурсов. Исключением является служба Application Insights, которая доступна не во всех тех расположениях, в которых доступны другие службы. Если вы выберете расположение, где эта служба не доступна, ее экземпляр будет создан в регионе "центрально-южная часть США".

* **Имя рабочего пространства** Службы машинного обучения Azure в удобном для пользователя формате.

    Имена для всех остальных служб создаются случайным образом.

> [!TIP]
> Хотя шаблон, связанный с этим документом, создает новый реестр контейнеров Azure, вы также можете создать новую рабочую область, не создавая реестр контейнеров. Если в рабочей области имеется реестр контейнеров, то он будет создан при выполнении операции, требующей наличия реестра контейнеров. Например, обучение или развертывание модели.
>
> Вы также можете сослаться на существующий реестр контейнеров или учетную запись хранения в шаблоне Azure Resource Manager, а не создать новый.

Дополнительные сведения о шаблонах вы найдете в следующих статьях.

* [Шаблоны диспетчера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-powershell.md)
* [Типы ресурсов Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Выполните инструкции из раздела [Развертывание ресурсов с помощью настраиваемого шаблона](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Дойдя до экрана __Изменить шаблон__, вставьте шаблон, настроенный при изучении этого раздела.
1. Щелкните __Сохранить__, чтобы применить этот шаблон. Укажите описанные ниже сведения и подтвердите согласие с предложенными условиями.

   * Подписка. Выберите подписку Azure, которая будет использоваться для этих ресурсов.
   * Группа ресурсов. Выберите или создайте группу ресурсов для хранения служб.
   * Имя рабочей области. имя, которое будет использоваться для создаваемой рабочей области Машинное обучение Azure. Для имени рабочего пространства допускается длина от 3 до 33 символов. Имя может содержать только буквы, цифры и дефис ("-").
   * Расположение. Выберите расположение, в котором будут создаваться ресурсы.

Дополнительные сведения см. в разделе [Развертывание ресурсов с помощью настраиваемого шаблона](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](../azure-resource-manager/secure-template-with-sas-token.md).

## <a name="use-azure-cli"></a>Использование Azure CLI

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure CLI](../azure-resource-manager/secure-template-with-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Политики доступа Azure Key Vault и шаблоны Azure Resource Manager

При использовании шаблона Azure Resource Manager для создания рабочей области и связанных с ней ресурсов (включая Azure Key Vault) несколько раз. Например, использование шаблона несколько раз с теми же параметрами, что и часть конвейера непрерывной интеграции и развертывания.

Большинство операций создания ресурсов через шаблоны идемпотентными, но Key Vault удаляет политики доступа каждый раз при использовании шаблона. Очистка политик доступа нарушает доступ к Key Vault для любой существующей рабочей области, которая ее использует. Например, может произойти сбой функции "отключить/создать" для виртуальных машин Azure Notebook.  

Чтобы избежать этой проблемы, рекомендуется использовать один из следующих подходов.

* Не развертывайте шаблон более одного раза для одних и тех же параметров. Или удалите существующие ресурсы перед использованием шаблона для их повторного создания.
  
* Изучите политики доступа Key Vault, а затем используйте эти политики, чтобы задать свойство `accessPolicies` шаблона. Чтобы просмотреть политики доступа, используйте следующую команду Azure CLI.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Дополнительные сведения об использовании раздела `accessPolicies` шаблона см. в [справочнике по объектам акцессполициентри](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Убедитесь, что Key Vault ресурс уже существует. Если это так, не создавайте его повторно с помощью шаблона. Например, чтобы использовать существующий Key Vault вместо создания нового, внесите следующие изменения в шаблон:

    * **Добавьте** параметр, который принимает идентификатор существующего Key Vault ресурса:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Удалите** раздел, который создает ресурс KEY Vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```
    
    * **Удалите** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` строку из раздела `dependsOn` рабочей области. Также **измените** запись `keyVault` в разделе `properties` рабочей области, чтобы она ссылалась на параметр `keyVaultId`:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```
      
    После внесения этих изменений можно указать идентификатор существующего Key Vault ресурса при запуске шаблона. Затем шаблон будет повторно использовать Key Vault, задав для свойства `keyVault` рабочей области его идентификатор.

    Чтобы получить идентификатор Key Vault, можно сослаться на выходные данные исходного шаблона Run или использовать Azure CLI. Следующая команда является примером использования Azure CLI для получения Key Vaultного идентификатора ресурса:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Эта команда возвращает значение следующего вида:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```



## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
