---
title: Создание рабочей области с помощью шаблона Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Сведения о том, как применить шаблон Azure Resource Manager для создания рабочей области Машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: eae10b7ae8cd14fd120e969c39c05a8ba2525003
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681549"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Создание рабочей области для Машинного обучения Azure с помощью шаблона Azure Resource Manager

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

В этой статье мы рассмотрим несколько способов создания рабочей области Машинного обучения Azure с помощью шаблонов Azure Resource Manager. Шаблон Resource Manager позволяет легко создать все ресурсы в отдельной скоординированной операции. Шаблон представляет собой документ JSON, в котором определены необходимые для развертывания ресурсы. Также здесь можно указать параметры развертывания. Эти параметры позволят предоставить входные значения при использовании шаблона.

Узнайте подробнее [о развертывании приложения с помощью шаблона диспетчера ресурсов Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас ее нет, используйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

* Чтобы использовать шаблон из командной строки, вам потребуется [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Чтобы создать рабочую область Машинного обучения Azure и связанные ресурсы Azure, примените следующий шаблон Azure Resource Manager.

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Этот шаблон создает следующие ресурсы Azure:

* группа ресурсов Azure.
* Учетная запись хранения Azure
* Azure Key Vault
* Azure Application Insights
* Реестр контейнеров Azure
* Рабочая область службы "Машинное обучение Azure"

Группа ресурсов выполняет функции контейнера, который содержит службы. Для рабочего пространства Службы машинного обучения Azure нужно несколько служб.

Наш пример шаблона принимает два параметра.

* **Расположение** указывает, где будут созданы группа ресурсов и службы.

    Этот шаблон будет использовать выбранное расположение для большинства ресурсов. Исключением является служба Application Insights, которая доступна не во всех тех расположениях, в которых доступны другие службы. Если вы выберете расположение, где эта служба не доступна, ее экземпляр будет создан в регионе "центрально-южная часть США".

* **Имя рабочего пространства** Службы машинного обучения Azure в удобном для пользователя формате.

    > [!NOTE]
    > В имени рабочей области не учитывается регистр.

    Имена для всех остальных служб создаются случайным образом.

> [!TIP]
> Хотя шаблон, связанный с этим документом, создает новый Реестр контейнеров Azure, вы можете создать рабочую область и без реестра контейнеров. Он будет создан автоматически при выполнении любой операции, для которой реестр контейнеров обязателен. Например, при обучении или развертывании модели.
>
> Вы также можете указать в шаблоне Azure Resource Manager существующий реестр контейнеров или учетную запись хранения, а не создавать новые.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Дополнительные сведения о шаблонах вы найдете в следующих статьях.

* [Шаблоны диспетчера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-powershell.md)
* [Типы ресурсов Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Расширенный шаблон

Создание рабочей области с тремя параметрами показано в следующем примере шаблона:

* режим высокой конфиденциальности для рабочей области;
* шифрование для рабочей области;
* использование существующего экземпляра Azure Key Vault для извлечения управляемых клиентом ключей.

Дополнительные сведения см. в статье [Шифрование данных при хранении](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Чтобы использовать этот шаблон, ваша подписка должна соответствовать некоторым дополнительным требованиям.
> * Приложение __Машинное обучение Azure__ должно быть __участником__ для подписки Azure.
> * Должен существовать хранилище Azure Key Vault с ключом шифрования.
> * Должна существовать политика доступа к Azure Key Vault, которая предоставляет права на __получение__, __упаковку__ и __распаковку__ приложению __Azure Cosmos DB__.
> * Хранилище Azure Key Vault должно размещаться в том же регионе, где вы намерены создать рабочую область Машинного обучения Azure.

Чтобы __добавить приложение Машинного обучения Azure в качестве участника__, используйте следующие команды:

1. Чтобы выполнить проверку подлинности в Azure, выполните следующую команду в интерфейсе командной строки:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Чтобы получить идентификатор объекта для приложения Машинного обучения Azure, выполните приведенную ниже команду. Это значение может быть разным для каждой подписки Azure.

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Эта команда возвращает глобально уникальный идентификатор объекта.

1. Чтобы добавить идентификатор объекта в подписку в качестве участника, используйте приведенную ниже команду. Замените `<object-ID>` значением GUID из предыдущего шага. Замените `<subscription-ID>` именем или идентификатором подписки Azure.

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Чтобы добавить ключ в Azure Key Vault__, используйте процедуру из раздела [Добавление ключа, секрета или сертификата в хранилище ключей](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) статьи __Управление Key Vault с помощью интерфейса командной строки Azure__.

__Чтобы добавить политику доступа к хранилищу ключей, выполните следующие команды:__

1. Чтобы получить идентификатор объекта для приложения Azure Cosmos DB, выполните приведенную ниже команду. Это значение может быть разным для каждой подписки Azure.

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Эта команда возвращает глобально уникальный идентификатор объекта.

1. Чтобы установить политику, используйте приведенную ниже команду. Замените `<keyvault-name>` именем в существующего хранилища Azure Key Vault. Замените `<object-ID>` значением GUID из предыдущего шага.

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Чтобы получить значения__ для параметров `cmk_keyvault` (идентификатор Key Vault) и `resource_cmk_uri` (URI ключа), которые нужны в этом шаблоне, выполните следующие действия.

1. Чтобы получить идентификатор Key Vault, используйте следующую команду:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Эта команда возвращает значение следующего вида: `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`.

1. Чтобы получить значение URI для управляемого клиентом ключа, используйте следующую команду:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Эта команда возвращает значение следующего вида: `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

__Пример шаблона__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> После создания рабочей области вы не сможете изменить параметры для конфиденциальных данных, шифрования, идентификатора хранилища ключей или идентификаторов ключей. Чтобы изменить эти значения, придется создать новую рабочую область с новыми значениями.

## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Выполните инструкции из раздела [Развертывание ресурсов с помощью настраиваемого шаблона](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Дойдя до экрана __Изменить шаблон__, вставьте шаблон, настроенный при изучении этого раздела.
1. Щелкните __Сохранить__, чтобы применить этот шаблон. Укажите описанные ниже сведения и подтвердите согласие с предложенными условиями.

   * Подписка: Выберите подписку Azure, которую нужно использовать для этих ресурсов.
   * Группа ресурсов: Выберите или создайте группу ресурсов, которая будет содержать службы.
   * Имя рабочей области: Это имя, которое будет присвоено созданному рабочему пространству Службы машинного обучения Azure. Для имени рабочего пространства допускается длина от 3 до 33 символов. Имя может содержать только буквы, цифры и дефис ("-").
   * Расположение. Выберите расположение, в котором будут созданы ресурсы.

Дополнительные сведения см. в разделе [Развертывание ресурсов с помощью настраиваемого шаблона](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Использование Azure CLI

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-cli.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="resource-provider-errors"></a>Ошибки поставщика ресурсов

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Политики доступа Azure Key Vault и шаблоны Azure Resource Manager

Шаблон Azure Resource Manager можно многократно применять для создания рабочей области и связанных с ней ресурсов (в том числе Azure Key Vault). Например, шаблон можно многократно применять с одинаковыми параметрами в составе конвейера непрерывной интеграции и развертывания.

Большинство операций создания ресурсов с использованием шаблонов являются идемпотентными, но Key Vault удаляет политики доступа каждый раз при использовании шаблона. Очистка политик доступа нарушает доступ к Key Vault из любой существующей рабочей области, которая его использует. Например, могут не выполняться функции остановки и создания для виртуальных машин Записных книжек Azure.  

Чтобы избежать этой проблемы, примените один из следующих подходов.

* Не развертывайте шаблон более одного раза с одинаковыми параметрами. Удаляйте существующие ресурсы перед тем, как создавать их повторно с помощью шаблона.

* Изучите политики доступа к Key Vault, а затем примените их для настройки свойства `accessPolicies` в шаблоне. Для просмотра политик доступа можно выполнить следующую команду Azure CLI.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Дополнительные сведения об использовании раздела `accessPolicies` шаблона см. в разделе [Объект AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry) справочника.

* Проверьте, существует ли ресурс Key Vault. Если существует, его не следует создавать повторно с помощью шаблона. Например, внесите следующие изменения в шаблон, чтобы использовать существующий ресурс Key Vault вместо создания нового:

    * **Добавьте** параметр, который принимает значение идентификатора существующего ресурса Key Vault:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Удалите** раздел, который создает ресурс Key Vault:

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

    * **Удалите** строку `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` из раздела `dependsOn` для рабочей области. Кроме того, **измените** запись `keyVault` в разделе `properties` для рабочей области, включив в нее ссылку на параметр `keyVaultId`:

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

    После этих изменений вы сможете указать идентификатор существующего ресурса Key Vault при запуске шаблона. Тогда этот шаблон применит указанное хранилище ключей, присвоив его идентификатор свойству `keyVault` рабочей области.

    Чтобы получить идентификатор Key Vault, можно указать ссылку на выходные данные от выполнения исходного шаблона или применить Azure CLI. Следующая команда является примером использования Azure CLI для получения идентификатора ресурса Key Vault.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Эта команда возвращает значение следующего вида:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/templates/deploy-rest.md).
* [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
